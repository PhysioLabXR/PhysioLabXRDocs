#############
PhysioLab\ :sup:`XR` + Unity P300 Speller Tutorial
#############

    Last Modified: 09/09/2023

.. contents:: Overview

*************
Introduction
*************

This tutorial will demonstrate how to use **PhysioLab\ :sup:`XR`** and **Unity** to implement a `P300 Speller <https://www.frontiersin.org/articles/10.3389/fnhum.2019.00261/full>`_
Brain Computer Interface (BCI) game.
The P300 Speller is a classic BCI game that uses the P300 Event-related potential (ERP) component to detect the user's intention.
The P300 is one of the most important Event-related potentials(ERP) components that is used to evaluate cognitive function, such as attention, working memory , and concentration.
In this tutorial we will use the **PhysioLab\ :sup:`XR`** as a real-time data acquisition platform and analysis tool, and use **Unity** as a stimulus presentation platform.


.. image:: media/PhysioLabXRP300SpellerDemo-IntroductionImage.png
    :width: 1080




*************
Unity Paradigm
*************

The Unity source code is available at: `pysiolabxr_p300speller_demo <https://github.com/HaowenWeiJohn/PysioLabXR_P300Speller_Demo.git>`_

.. note::
    The Unity Paradigm is developed using `Unity 2021.3.27f1 <https://unity3d.com/get-unity/download/archive>`_


This Unity paradigm implements a classic P300 Speller BCI game and researchers can use it for other testing purposes.
The user will first train the system by focusing on the target alphabet and the system will record the EEG data and the corresponding target alphabet in the Train mode.
After the training process, the user can use the system to spell words by focusing on the target alphabet in Test mode.



=======
Lab Streaming Layer (LSL) Configuration
=======
There is a 6 channel LSL stream from Unity Paradigm to RenaScript and the description for each channel is shown below:

P300 Speller Game Unity  LSL  Configuration:

    **LSL Outlet: "PhysioLabXRP300SpellerDemoPredictionProbability"**

        *Channel[0]: (StateEnterExitMarker)*
            Indicates the selected game states (Train/Test/Interrupt)
                - StartState: 1
                - TrainIntroductionState: 2
                - TrainState: 3
                - TestIntroductionState: 4
                - TestState: 5
                - EndState: 6
            send corresponding negative number when exiting the state



        *Channel[1]: (FlashBlockStartEndMarker)*
            Indicates the start and end of flashing for each trail
                - FlashBlockStartEndMarker: 1
            send -1 when the flash block ends


        *Channel[2]: (FlashingMarker)*
            The flash event marker. Send 1 while any column or row turns on
                - FlashingMarker: 1

        *Channel[3]: (FlashingItemIndexMarker)*
            Indicate the identity of the flashing component, range from 0 - 11
                - 0 - 5 indicates row 0 - 5
                - 7 - 11 indicates column 0 - 5



        *Channel[4]: (FlashingTargetMarker)*
            Indicate if the component is target or non-target during the training process. This channel is zero during the testing mode.
                - Target: 1


        *Channel[5]: (StateInterruptMarker)*
            The interrupt event marker. Send 1 while the user presses the interrupt button (Esc). The game will return to the start state after the interrupt.
                - InterruptMarker: 1

The Channel will be zero when the corresponding event is not happening,
while Channel[3] will be zero while the flashing component is the first row.
*Therefore, we have to check Channel[2] and Channel[3] at the same time to determine the identity of the flashing component!*



=======
State Diagram
=======


Train State
************
.. image:: media/PhysioLabXRP300SpellerDemo-TrainStateDiagram.png
    :width: 2560


Test State
************

.. image:: media/PhysioLabXRP300SpellerDemo-TestStateDiagram.png
    :width: 2560


Flash Block
***********
.. image:: media/PhysioLabXRP300SpellerDemo-FlashBlockBoardController.png
    :width: 900
The Flash Block is located in the Test State and Train State.


The source code for the training and testing process is located in the `BoardController.cs <https://github.com/HaowenWeiJohn/PysioLabXR_P300Speller_Demo/blob/main/Assets/Scripts/Others/BoardController.cs>`_ file.
The
`TrainStateBoardCoroutine() <https://github.com/HaowenWeiJohn/PysioLabXR_P300Speller_Demo/blob/963f997522d68d43198caec110388f1886419307/Assets/Scripts/Others/BoardController.cs#L123>`_
and
`TestStateBoardCoroutine() <https://github.com/HaowenWeiJohn/PysioLabXR_P300Speller_Demo/blob/963f997522d68d43198caec110388f1886419307/Assets/Scripts/Others/BoardController.cs#L203>`_
are the main functions for the training and testing process.

Those two **coroutines** will be started when the game enters the Train State and Test State.

*************
PhysioLab\ :sup:`XR` Scripting
*************


=======
Lab Streaming Layer (LSL) Configuration
=======

There is a 1 channel LSL stream from the PhysioLab\ :sup:`XR` to Unity to transfer the prediction probability for each charactor from the PhysioLab\ :sup:`XR` to Unity.

PhysioLab\ :sup:`XR` LSL Configuration:

    **LSL Outlet: "PhysioLabXRP300SpellerDemoPredictionProbability"**

        *Channel[0]-Channel[35]: (PredictionProbability)*
            The prediction probability for each charactor.
            The prediction probability is a 36 element array that contains the prediction probability for each charactor.



=======
Scripting
=======

The script can be found at: `PhysioLabXRP300SpellerDemoScript.py <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/scripting/Examples/PhysioLabXR_P300Speller_Demo/PhysioLabXRP300SpellerDemoScript.py>`_

    .. code-block:: python

        # This is a demo script for the PhysioLabXR P300 Speller Game

        import numpy as np
        from physiolabxr.scripting.RenaScript import RenaScript
        from physiolabxr.utils.buffers import DataBuffer
        from imblearn.over_sampling import SMOTE
        from sklearn.linear_model import LogisticRegression
        from sklearn.model_selection import train_test_split
        from sklearn.metrics import f1_score
        from sklearn import metrics

        # import the config file
        from physiolabxr.scripting.Examples.PhysioLabXR_P300Speller_Demo.PhysioLabXRP300SpellerDemoConfig import *


        # matplotlib and seaborn are not installed in PhysioLabXR by default, so we need to check if they are installed before importing them
        # If you want to use matplotlib and seaborn in your script, please install them in the PhysioLabXR environment following the instructions in the PhysioLabXR documentation
        Plot = True
        try:
            import seaborn as sns
            import matplotlib.pyplot as plt
        except:
            Plot = False
            print("Seaborn and Matplotlib not installed. Skip Plot.")


        class PhysioLabXRGameP300SpellerDemoScript(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)
                # test network
                self.EXPERIMENT_STATE = ExperimentStateMarker.StartState
                self.IN_FLASHING_BLOCK = False
                self.model = LogisticRegression()
                self.data_buffer = DataBuffer()

                self.train_state_x = []
                self.train_state_y = []

                self.StateEnterExitMarker = 0
                self.FlashBlockStartEndMarker = 0
                self.FlashingMarker = 0
                self.FlashingItemIndexMarker = 0
                self.FlashingTargetMarker = 0
                self.StateInterruptMarker = 0



            # Start will be called once when the run button is hit.
            def init(self):
                print('Init function is called')
                pass

            # loop is called <Run Frequency> times per second
            def loop(self):
                # print('Loop function is called')
                if EEG_STREAM_NAME not in self.inputs.keys() or EVENT_MARKER_CHANNEL_NAME not in self.inputs.keys():
                    # if no event marker or no eeg stream, we do not do anything
                    print('No EEG stream or no event marker stream, return')
                    # state is None, and Flashing is False. We interrupt the experiment
                    self.EXPERIMENT_STATE = None
                    self.IN_FLASHING_BLOCK = False
                    return

                event_marker_data = self.inputs.get_data(EVENT_MARKER_CHANNEL_NAME)
                event_marker_timestamps = self.inputs.get_timestamps(EVENT_MARKER_CHANNEL_NAME)
                # print(event_marker_data)
                # in this example, we only care about the Train, Test, Interrupt, and Block Start/Block end markers
                # process event markers
                # try:
                for event_index, event_marker_timestamp in enumerate(event_marker_timestamps):
                    event_marker = event_marker_data[:, event_index]

                    self.StateEnterExitMarker = event_marker[EventMarkerChannelInfo.StateEnterExitMarker]
                    self.FlashBlockStartEndMarker = event_marker[EventMarkerChannelInfo.FlashBlockStartEndMarker]
                    self.FlashingMarker = event_marker[EventMarkerChannelInfo.FlashingMarker]
                    self.FlashingItemIndexMarker = event_marker[EventMarkerChannelInfo.FlashingItemIndexMarker]
                    self.FlashingTargetMarker = event_marker[EventMarkerChannelInfo.FlashingTargetMarker]
                    self.StateInterruptMarker = event_marker[EventMarkerChannelInfo.StateInterruptMarker]

                    if self.StateInterruptMarker:
                        # state is None, and Flashing is False. We interrupt the experiment
                        self.EXPERIMENT_STATE = None
                        self.IN_FLASHING_BLOCK = False

                    elif self.StateEnterExitMarker:
                        self.switch_state(self.StateEnterExitMarker)

                    elif self.FlashBlockStartEndMarker:
                        print('Block Start/End Marker: ', self.FlashBlockStartEndMarker)

                        if self.FlashBlockStartEndMarker == 1:  # flash start
                            self.IN_FLASHING_BLOCK = True
                            print('Start Flashing Block')
                            self.inputs.clear_up_to(event_marker_timestamp)
                            # self.data_buffer.update_buffers(self.inputs.buffer)
                        if self.FlashBlockStartEndMarker == -1:  # flash end
                            self.IN_FLASHING_BLOCK = False
                            print('End Flashing Block')
                            if self.EXPERIMENT_STATE == ExperimentStateMarker.TrainState:
                                # train callback
                                self.train_callback()
                                pass
                            elif self.EXPERIMENT_STATE == ExperimentStateMarker.TestState:
                                # test callback
                                self.test_callback()
                                pass
                    elif self.FlashingMarker:  # flashing
                        print('Flashing Marker: ', self.FlashingMarker)
                        print('Flashing Target Marker: ', self.FlashingTargetMarker)
                        print('Flashing Item Index Marker: ', self.FlashingItemIndexMarker)
                    else:
                        pass
                # except Exception as e:
                #     print(e)
                #     return

                # if flashing
                if self.IN_FLASHING_BLOCK:
                    # the event marker in the buffer only contains the event marker in the current flashing block
                    self.data_buffer.update_buffers(self.inputs.buffer)
                    # print('In Flashing Block, save data to buffer')

                self.inputs.clear_buffer_data()


            def switch_state(self, new_state):
                if new_state == ExperimentStateMarker.StartState:
                    print('Start State')
                    self.EXPERIMENT_STATE = ExperimentStateMarker.StartState

                elif new_state == ExperimentStateMarker.TrainIntroductionState:
                    print('Train Introduction State')
                    self.EXPERIMENT_STATE = ExperimentStateMarker.TrainIntroductionState

                elif new_state == ExperimentStateMarker.TrainState:
                    print('Train State')
                    self.EXPERIMENT_STATE = ExperimentStateMarker.TrainState

                elif new_state == ExperimentStateMarker.TestIntroductionState:
                    print('Test Introduction State')
                    self.EXPERIMENT_STATE = ExperimentStateMarker.TestIntroductionState

                elif new_state == ExperimentStateMarker.TestState:
                    print('Test State')
                    self.EXPERIMENT_STATE = ExperimentStateMarker.TestState

                elif new_state == ExperimentStateMarker.EndState:
                    print('End State')
                    self.EXPERIMENT_STATE = ExperimentStateMarker.EndState

                else:
                    print('Exit Current State: ', new_state)
                    self.EXPERIMENT_STATE = None

            def train_callback(self):
                # train callback


                flash_timestamps = self.data_buffer.get_timestamps(EVENT_MARKER_CHANNEL_NAME)
                eeg_timestamps = self.data_buffer.get_timestamps(EEG_STREAM_NAME)
                eeg_epoch_start_indices = np.searchsorted(eeg_timestamps, flash_timestamps, side='left')

                sample_before_epoch = np.floor(EEG_EPOCH_T_MIN * EEG_SAMPLING_RATE).astype(int)
                sample_after_epoch = np.floor(EEG_EPOCH_T_MAX * EEG_SAMPLING_RATE).astype(int)
                for eeg_epoch_start_index in eeg_epoch_start_indices:
                    eeg_epoch = self.data_buffer.get_data(EEG_STREAM_NAME)[:, eeg_epoch_start_index+sample_before_epoch:eeg_epoch_start_index+sample_after_epoch]
                    self.train_state_x.append(eeg_epoch)

                labels = self.data_buffer.get_data(EVENT_MARKER_CHANNEL_NAME)[EventMarkerChannelInfo.FlashingTargetMarker, :]
                self.train_state_y.extend(labels)

                # train based on all the data in the buffer
                x = np.array(self.train_state_x)
                y = np.array(self.train_state_y)
                print("Train On Data: ", x.shape, y.shape)
                train_logistic_regression(x, y, self.model, test_size=0.1)
                self.data_buffer.clear_buffer_data() # clear the buffer data for the next flashing block
                pass

            def test_callback(self):
                # test callback

                x = []

                flash_timestamps = self.data_buffer.get_timestamps(EVENT_MARKER_CHANNEL_NAME)
                eeg_timestamps = self.data_buffer.get_timestamps(EEG_STREAM_NAME)
                eeg_epoch_start_indices = np.searchsorted(eeg_timestamps, flash_timestamps, side='left')

                sample_before_epoch = np.floor(EEG_EPOCH_T_MIN * EEG_SAMPLING_RATE).astype(int)
                sample_after_epoch = np.floor(EEG_EPOCH_T_MAX * EEG_SAMPLING_RATE).astype(int)

                for eeg_epoch_start_index in eeg_epoch_start_indices:
                    eeg_epoch = self.data_buffer.get_data(EEG_STREAM_NAME)[:, eeg_epoch_start_index+sample_before_epoch:eeg_epoch_start_index+sample_after_epoch]
                    x.append(eeg_epoch)

                # predict based on all the data in the buffer
                x = np.array(x)
                x = x.reshape(x.shape[0], -1)
                y_target_probabilities = self.model.predict_proba(x)[:, 1]
                print(y_target_probabilities)
                flashing_item_indices = self.data_buffer.get_data(EVENT_MARKER_CHANNEL_NAME)[EventMarkerChannelInfo.FlashingItemIndexMarker, :]
                flashing_item_indices = np.array(flashing_item_indices).astype(int)
                probability_matrix = np.zeros(shape=np.array(Board).shape)
                for flashing_item_index, y_target_probability in zip(flashing_item_indices, y_target_probabilities):
                    if flashing_item_index<=5: # this is row index
                        row_index = flashing_item_index
                        probability_matrix[row_index, :] += y_target_probability
                    else: # this is column index, we need -6 to get the column index
                        column_index = flashing_item_index-6
                        probability_matrix[:, column_index] += y_target_probability

                # normalize the probability matrix to 0 to 1
                probability_matrix = probability_matrix / len(flashing_item_indices/24)


                self.set_output(PREDICTION_PROBABILITY_CHANNEL_NAME, probability_matrix.flatten())
                print("Prediction Probability Sent")

                self.data_buffer.clear_buffer_data()


                print(probability_matrix)

                # plot the probability matrix
                if Plot:
                    plt.imshow(probability_matrix, cmap='hot', interpolation='nearest')
                    plt.show()



            # cleanup is called when the stop button is hit
            def cleanup(self):
                self.model = None
                print('Cleanup function is called')

        def train_logistic_regression(X, y, model, test_size=0.2):
            """
            Trains a logistic regression model on the input data and prints the confusion matrix.

            Args:
                X (np.ndarray): Input features.
                y (np.ndarray): Target variable.
                model (LogisticRegression): An instance of LogisticRegression from scikit-learn.
                test_size (float): Proportion of the data to reserve for testing. Default is 0.2.

            Returns:
                None.

            Raises:
                TypeError: If model is not an instance of LogisticRegression.
                ValueError: If test_size is not between 0 and 1.

            """
            # Check if model is an instance of LogisticRegression
            if not isinstance(model, LogisticRegression):
                raise TypeError("model must be an instance of LogisticRegression.")

            # Check if test_size is between 0 and 1
            if test_size <= 0 or test_size >= 1:
                raise ValueError("test_size must be between 0 and 1.")

            # Split the data into training and testing sets
            x_train, x_test, y_train, y_test = train_test_split(X, y, stratify=y, test_size=test_size)
            rebalance_classes(x_train, y_train, by_channel=True)

            # Reshape the data. This is simple logistic regression, so we flatten the input x
            x_train = x_train.reshape(x_train.shape[0], -1)
            x_test = x_test.reshape(x_test.shape[0], -1)

            # Fit the model to the training data and make predictions on the test data
            model.fit(x_train, y_train)
            y_pred = model.predict(x_test)

            # plot the confusion matrix

            confusion_matrix(y_test, y_pred)

        def confusion_matrix(y_test: np.ndarray, y_pred: np.ndarray) -> None:
            """
            Plots a confusion matrix for the predicted vs. actual labels and prints the accuracy score.

            Args:
                y_test (np.ndarray): Actual labels of the test set.
                y_pred (np.ndarray): Predicted labels of the test set.

            Returns:
                None.

            Raises:
                TypeError: If either y_test or y_pred are not numpy arrays.

            """
            # Check if y_test and y_pred are numpy arrays
            if not isinstance(y_test, np.ndarray) or not isinstance(y_pred, np.ndarray):
                raise TypeError("y_test and y_pred must be numpy arrays.")

            # Calculate the confusion matrix and f1 score
            cm = metrics.confusion_matrix(y_test, y_pred)
            print("Confusion Matrix:")
            print(cm)
            score = f1_score(y_test, y_pred, average='macro')
            print("F1 Score (Macro): {:.2f}".format(score))

            if Plot:
                # Create a heatmap of the confusion matrix
                plt.figure(figsize=(9, 9))
                sns.heatmap(cm, annot=True, fmt=".3f", linewidths=.5, square=True, cmap='Blues_r')
                plt.ylabel('Actual label')
                plt.xlabel('Predicted label')
                all_sample_title = 'Accuracy Score: {0}'.format(score)
                plt.title(all_sample_title, size=15)
                plt.show()

        def rebalance_classes(x, y, by_channel=False):
            """
            Resamples the data to balance the classes using SMOTE algorithm.

            Parameters:
                x (np.ndarray): Input data array of shape (epochs, channels, samples).
                y (np.ndarray): Target labels array of shape (epochs,).
                by_channel (bool): If True, balance the classes separately for each channel. Otherwise,
                    balance the classes for the whole input data.

            Returns:
                tuple: A tuple containing the resampled input data and target labels as numpy arrays.
            """
            epoch_shape = x.shape[1:]

            if by_channel:
                y_resample = None
                channel_data = []
                channel_num = epoch_shape[0]

                # Loop through each channel and balance the classes separately
                for channel_index in range(0, channel_num):
                    sm = SMOTE(k_neighbors=5, random_state=42)
                    x_channel = x[:, channel_index, :]
                    x_channel, y_resample = sm.fit_resample(x_channel, y)
                    channel_data.append(x_channel)

                # Expand dimensions for each channel array and concatenate along the channel axis
                channel_data = [np.expand_dims(x, axis=1) for x in channel_data]
                x = np.concatenate([x for x in channel_data], axis=1)
                y = y_resample

            else:
                # Reshape the input data to 2D array and balance the classes
                x = np.reshape(x, newshape=(len(x), -1))
                sm = SMOTE(random_state=42)
                x, y = sm.fit_resample(x, y)

                # Reshape the input data back to its original shape
                x = np.reshape(x, newshape=(len(x),) + epoch_shape)

            return x, y

        # END CLASS

The configuration file can be found in
`PhysioLabXR_P300SpellerDemoConfig.py <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/scripting/Examples/PhysioLabXR_P300Speller_Demo/PhysioLabXRP300SpellerDemoConfig.py>`_.

    .. code-block:: python

        from enum import Enum

        import numpy as np

        # OpenBCI Stream Name
        EEG_STREAM_NAME = 'OpenBCICyton8Channels'
        EVENT_MARKER_CHANNEL_NAME = 'PhysioLabXRP300SpellerDemoEventMarker'
        PREDICTION_PROBABILITY_CHANNEL_NAME = "PhysioLabXRP300SpellerDemoPredictionProbability"

        # Sampling Rate
        EEG_SAMPLING_RATE = 250

        # eeg channel number
        EEG_CHANNEL_NUM = 8

        # epoch configuration
        EEG_EPOCH_T_MIN = -0.2
        EEG_EPOCH_T_MAX = 1.0


        # eeg channels from cython 8 board
        EEG_CHANNEL_NAMES = [
            "Fz",
            "Cz",
            "Pz",
            "C3",
            "C4",
            "P3",
            "P4",
            "O1"
        ]


        class IndexClass(int, Enum):
            pass

        class EventMarkerChannelInfo(IndexClass):
            StateEnterExitMarker = 0,
            FlashBlockStartEndMarker = 1,
            FlashingMarker = 2,
            FlashingItemIndexMarker = 3,  # the 0 - 5 is row, 7 - 11 is column
            FlashingTargetMarker = 4,
            StateInterruptMarker = 5,

        class ExperimentStateMarker(IndexClass):
            StartState = 1,
            TrainIntroductionState = 2,
            TrainState = 3,
            TestIntroductionState = 4,
            TestState = 5,
            EndState = 6,

            # # this is not included in the unity paradigm
            # IDLEState = 7


        ROW_FLASH_MARKER_LIST = [0, 1, 2, 3, 4, 5]
        COL_FLASH_MARKER_LIST = [7, 8, 9, 10, 11]
        Target_Flash_Marker = 1
        Non_Target_Flash_Marker = 0

        Board = [['A', 'B', 'C', 'D', 'E', 'F'],
                 ['G', 'H', 'I', 'J', 'K', 'L'],
                 ['M', 'N', 'O', 'P', 'Q', 'R'],
                 ['S', 'T', 'U', 'V', 'W', 'X'],
                 ['Y', 'Z', '0', '1', '2', '3'],
                 ['4', '5', '6', '7', '8', '9']]

The configuration file is located in the same directory with the script file, and all the configurations
should be imported into the script file. The configuration file includes the following configurations:

    * **EEG_STREAM_NAME**: The name of the EEG stream.
    * **EVENT_MARKER_CHANNEL_NAME**: The name of the event marker channel.
    * **PREDICTION_PROBABILITY_CHANNEL_NAME**: The name of the prediction probability channel.
    * **EEG_SAMPLING_RATE**: The sampling rate of the EEG stream.
    * **EEG_CHANNEL_NUM**: The number of EEG channels.
    * **EEG_EPOCH_T_MIN**: The minimum time of the epoch.
    * **EEG_EPOCH_T_MAX**: The maximum time of the epoch.
    * **EEG_CHANNEL_NAMES**: The names of the EEG channels.
    * **EventMarkerChannelInfo**: The class that defines the event marker channel information.
    * **ExperimentStateMarker**: The class that defines the experiment state marker.
    * **ROW_FLASH_MARKER_LIST**: The list of the row flash marker.
    * **COL_FLASH_MARKER_LIST**: The list of the column flash marker.
    * **Target_Flash_Marker**: The target flash marker.
    * **Non_Target_Flash_Marker**: The non-target flash marker.
    * **Board**: The board of the P300 speller.

Now we will go through each function in the script.






*************
Experiment
*************

This experiment implemented the P300 Speller Brain Computer Interface using RenaLabApp and a customized Unity Paradigm.
The similar setup can be extended to other human subject studies that include real time data acquisition and customized
experiment environment. The P300 is one of the most important Event-related potentials(ERP)
components that is used to evaluate cognitive function, such as attention, working memory , and concentration.
A peak occurs 300 ms after the expected event happened.

The Unity Paradigm hosts a six by five board that includes 26 alphabets, from A to Z, and 10 numbers, from 0 to 9.


During the training period, the user is instructed to focus on a particular letter(instructed in green),
and each roll and column will be flashed n times; therefore, there are n x 12 samples for flash block.
After the training mode, the user can go to the testing mode. Similarly, the user will focus on one character
during each trail, but without instruction. The predicted result will be typed in the text input box.

=======
Requirements
=======

1. PhysioLab\ :sup:`XR`
2. Unity Platform
3. OpenBCI Cyton Board
    Channel Selection: Fz, Cz, Pz, C3, C4, P3, P4, O1.



=======
Experiment Setup
=======


=======
Run Experiment
=======

Practice
    He/She can practice the experiment in the Practice mode which does not interact with the RenaScript.

Train
    The first step is to collect some training data for the Logistic Regression model. The default training session asks the user to spell { "R"  "E" "N"  "A" }, and this can be modified in the options window.  The flashing pattern will be the same as the Practice Mode. After each trial, the Unity interface will wait for the training finish signal from RenaLabApp Script and the training accuracy will be shown on the screen. (The timeout duration will be 2 seconds by default).

Test
    After the training stage, he/she can test the trained model by clicking the test button. The test trial will run recursively until the user clicks the interrupt button.Unity will wait for the prediction result from RenaLabApp at the end of each trial, and the board will show the prediction result as well as the text input box on top. (The timeout duration will be 2 seconds by default, same as the training mode ).



*Run the experiment without OpenBCI device and Unity*
    We provide users with a recorded experiment that can be replayed while the user starts the replay.

