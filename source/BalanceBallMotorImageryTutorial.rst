###################################
Balance Ball Tutorial
###################################
    Last Modified: 10/19/2023

.. contents:: Overview

*************
Introduction
*************

This tutorial will show you how to use PhysioLab\ :sup:`XR` and Unity to set up a simple balance ball game that
you can play by only imagining your left or right hand movements.
In this tutorial we will use the PhysioLabXR as a real-time data acquisition platform and analysis tool, and
use Unity as a stimulus presentation platform.

.. figure:: media/BBall_play.png
   :width: 400
   :align: center
   :alt: balance ball

*************
Unity Paradigm
*************

The Unity source code is available at: `BalancingBallGame script <https://github.com/ApocalyVec/ReNaApp_BalancingBallGame>`_.

.. note::
    The Unity Paradigm is developed using `Unity 2021.3.27f1 <https://unity3d.com/get-unity/download/archive>`_


This Unity paradigm implements a balance ball BCI game. After a quick training session, The user will
be able to play the game with a BCI device in real time.


=======
Lab Streaming Layer (LSL) Configuration
=======
There is a single-channel LSL stream from Unity Paradigm to RenaScript. The description for each channel is shown below:

P300 Speller Game Unity  LSL  Configuration:

    **LSL Outlet: "EventMarker_BallGame"**

        *Channel[0]: (EventMarker_BallGame)*
            Indicates the selected game states (Train/Evaluation)
                - TrainStart = 1, TrainEnd = -1
                - LeftHandTrialStart = 2, LeftHandTrialEnd = -2
                - RightHandTrialStart = 3, RightHandTrialEnd = -3
                - EvalStart = 6, EvalEnd = -6


=======
State Diagram
=======

.. figure:: media/balanceBall_train_diagram.png
   :width: 400
   :align: center
   :alt: balance ball

.. figure:: media/balanceBall_eval_diagram.png
   :width: 400
   :align: center
   :alt: balance ball


Train State
************


*************
PhysioLab\ :sup:`XR` Scripting
*************


=======
Lab Streaming Layer (LSL) Configuration
=======

There is a single-channel LSL stream from the PhysioLab\ :sup:`XR` to Unity to transfer the predicted side of hand movements from the PhysioLab\ :sup:`XR` to Unity.

PhysioLab\ :sup:`XR` LSL Configuration:

    **LSL Outlet: "EventMarker_BallGame"**

        *Channel[0]: (EventMarker_BallGame)*
            Indicates the predicted side of hand movements (Left/Right)
                - Left = 2
                - Right = 3



=======
Script: MotorImageryBalanceBall.py
=======

The script can be found at: `MotorImageryBalanceBall.py <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/scripting/Examples/PhysioLabXR_Balanceball_Demo/MotorImageryBalanceBall.py">`_

The configuration file includes the following configurations:

    * **EEG_STREAM_NAME**: The name of the EEG stream.
    * **EVENT_MARKER_CHANNEL_NAME**: The name of the event marker channel.


*************
Experiment
*************

This experiment implemented the Balance Ball Brain Computer Interface using RenaLabApp and a customized Unity Paradigm.
The similar setup can be extended to other human subject studies that include continuous stream of real time EEG data and customized
experiment environment.

In the training session, the user will be asked to imagine left or right hand movements while
looking at an automated ball moving on the screen. After the training session, Unity will communicate with the python
script XXXX to fit a predictive model based on the training data.

The balance ball game has the following features implemented:
- a score counter that keeps track of the remaining lives of the ball (3 lives)
- a platform that the ball can roll on
- a ball that will naturally roll down to the side of the platform that is being pressed. When the ball
    reaches the edge of the platform, it will fall off and deduct one life from the player
- randomly spawned black collectible cubes that the ball can pick up by rolling over

=======
Requirements
=======

1. PhysioLab\ :sup:`XR`: `physiolabxr <https://github.com/PhysioLabXR/PhysioLabXR/tree/master>`_
2. Unity project download from: `PhysioLabXR_Balance_Ball_Demo <https://github.com/ApocalyVec/ReNaApp_BalancingBallGame>`_
3. OpenBCI: `Cyton-8-Channel <https://shop.openbci.com/collections/frontpage/products/cyton-biosensing-board-8-channel?variant=38958638540>`_
    Channel Selection: F3, Fz, F4, C3, Cz, C4, P3, P4.

=======
Experiment Setup
=======
In this experiment, all the required scripts are included in your local directory: physiolabxr/scripting/Examples/PhysioLabXR_BalanceBall_Demo.

Get the OpenBCI Cyton-8-Channel board and connect it to the computer.
For this step, please refer to: `OpenBCI Cyton Getting Started Guide <https://docs.openbci.com/GettingStarted/Boards/CytonGS/>`_. It is very important to complete the `FTDI Driver Installation <https://docs.openbci.com/Troubleshooting/FTDI_Fix_Windows/>`_ before starting the experiment. The Latency timer should be set to 1 ms (the default value is 16 ms) to reduce the latency.

Check EEG Signal Quality
************

You can use the OpenBCI GUI to check the EEG signal quality. Same as the previous step, please refer to `OpenBCI Cyton Getting Started Guide <https://docs.openbci.com/GettingStarted/Boards/CytonGS/>`_ to use OpenBCI GUI to check the impedance of each channel.

.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video8" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/PhysioLabXRP300SpellerDemo-OpenBCICyton8ChannelsCheckSignalQuality.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>


Start the OpenBCI Cyton-8-Channel board from PhysioLab\ :sup:`XR` Scripting Interface using PhysioLabXROpenBCICyton8ChannelsScript.py
************

The script can be downloaded from `PhysioLabXROpenBCICyton8ChannelsScript.py <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/scripting/Examples/PhysioLabXR_P300Speller_Demo/PhysioLabXROpenBCICyton8ChannelsScript.py>`_.

    .. code-block:: python

        # This is an example script for PhysioLabXR. It is a simple script that reads data from OpenBCI Cyton 8 Channels and sends it to Lab Streaming Layer.
        # The output stream name is "OpenBCICyton8Channels"


        import time

        import brainflow
        import pylsl
        from brainflow.board_shim import BoardShim, BrainFlowInputParams

        from physiolabxr.scripting.RenaScript import RenaScript


        class PhysioLabXROpenBCICyton8ChannelsScript(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)


            # Start will be called once when the run button is hit.
            def init(self):
                # check if the parameters are set

                if "serial_port" not in self.params: # check
                    while True:
                        print("serial_port is not set. Please set it in the parameters tab (e.g. COM3)")
                        time.sleep(1)
                else:
                    if type(self.params["serial_port"]) is not str:
                        while True:
                            print("serial_port should be a string (e.g. COM3)")
                            time.sleep(1)



                print("serial_port: ", self.params["serial_port"])

                # try init board
                self.brinflow_input_params = BrainFlowInputParams()

                # assign serial port from params to brainflow input params
                self.brinflow_input_params.serial_port = self.params["serial_port"]

                self.brinflow_input_params.ip_port = 0
                self.brinflow_input_params.mac_address = ''
                self.brinflow_input_params.other_info = ''
                self.brinflow_input_params.serial_number = ''
                self.brinflow_input_params.ip_address = ''
                self.brinflow_input_params.ip_protocol = 0
                self.brinflow_input_params.timeout = 0
                self.brinflow_input_params.file = ''

                # set board id to Cyton 8-channel (0)
                self.board_id = 0 # Cyton 8-channel

                try:
                    self.board = BoardShim(self.board_id, self.brinflow_input_params)
                    self.board.prepare_session()
                    self.board.start_stream(45000, '') # 45000 is the default and recommended ring buffer size
                    print("OpenBCI Cyton 8 Channels. Sensor Start.")
                except brainflow.board_shim.BrainFlowError:
                    while True:
                        print('Board is not ready. Start Fild. Please check the serial port and try again.')
                        time.sleep(1)


            # loop is called <Run Frequency> times per second
            def loop(self):
                timestamp_channel = self.board.get_timestamp_channel(0)
                eeg_channels = self.board.get_eeg_channels(0)
                # print(timestamp_channel)
                # print(eeg_channels)

                data = self.board.get_board_data()

                timestamps = data[timestamp_channel]
                data = data[eeg_channels]

                absolute_time_to_lsl_time_offset = time.time() - pylsl.local_clock()
                timestamps = timestamps - absolute_time_to_lsl_time_offset # remove the offset between lsl clock and absolute time
                self.set_output(stream_name="OpenBCICyton8Channels", data=data, timestamp=timestamps)


            # cleanup is called when the stop button is hit
            def cleanup(self):
                print('Stop OpenBCI Cyton 8 Channels. Sensor Stop.')


1. Go to the `Script Tab <Scripting.html>`_ and click the *Add* button to start the script. You can either create a new script and replace the content provided above, or select *PhysioLabXROpenBCICyton8ChannelsScript.py* located in the *physiolabxr/scripting/Examples/PhysioLabXR_P300Speller_Demo* directory. After adding the script, you will need to add the output stream in the *Output Widget* and parameters in the *Parameters Widget*.

2. Type the output stream name: *OpenBCICyton8Channels* in the *Output Widget* and click the *Add* button.

3. Keep the output type as *LSL* and *float32* and change the output channel number in the line edit to *8*. (We have 8 EEG channels in this experiment)

4. Type the parameter name: *serial_port* in the **Parameter Widget** and click the *Add* button.

5. Change the parameter type to *str* and type the serial port name in the line edit. (e.g. COM3) You can find this information in your device manager.

6. Below the text box with the path to your script, change the *Run Frequency (times per seconds)* to *>=30* Hz. (Higher frequency is recommended to reduce the latency, but the execution time for each loop also should be considered. Because this demo requires real-time data streaming, we sacrifice frequency for less latency.) Set the *Input Buffer Duration* to be 10.

7. Click the *Run* button to start the script.

Start Unity
************

1. Download the Unity project from the `Balance Ball Game <https://github.com/ApocalyVec/ReNaApp_BalancingBallGame>`_ repository.

2. Start the Game by clicking the *Play* button in the Unity Editor. This will initiate the **EventMarker_BallGame** on Network. (You can also build the project and run the executable file.)

3. Go to **Stream Tab**. Type **EventMarker_BallGame** in the *Add Widget* and click the *Start Button* to start the stream.


Add PysioLabP300SpellerDemoScript.py
************

1. Go to the `Script Tab <Scripting.html>`_ and click the *Add* button to start the script. You can either create a new script and replace with *MotorImageryBalanceBall.py* we mentioned above, or select *MotorImageryBalanceBall.py* located in the *physiolabxr/scripting/Examples/PhysioLabXR_BalanceBall_Demo* directory.

2. We need to add the Event Marker stream and EEG Stream as an input to the script. Type the stream name: *OpenBCICyton8Channels* in the *Input Widget* and click the *Add* button. Repeat this step for the *EventMarker_BallGame* stream.

3. Type the parameter name *MotorImageryInference* in the *Outputs Widget* and click the *Add* button. Keep the output type as *LSL* and *float32* and change the output channel number in the line edit to *1*. The predicted result is either Left (2) or Right (3), so we only need one channel to send the result.

4. Below the text box with the path to your script, change the *Run Frequency (times per seconds)* to *>=15* Hz.

5. Click the *Run* button to start the script.

6. Now you can add the *MotorImageryInference* stream in the **Stream Tab** and click the *Start Button* to start the stream.


====================
Experiment
====================

At this point, we have two scripts running in the **Script Tab**

1. *MotorImageryBalanceBall.py*: This script receives the *OpenBCICyton8Channels* stream from the OpenBCI Cyton 8 Channels and *EventMarker_BallGame* stream from the Unity platform. It also sends the *MotorImageryInference* stream to the Unity platform and *Stream Tab* just for visualization purpose.

2. *PhysioLabXROpenBCICyton8ChannelsScript.py*: This script connect the OpenBCI Cyton 8 Channels via `brainflow <https://brainflow.readthedocs.io/en/stable/>`_ and send the *OpenBCICyton8Channels* stream to the local network through LSL.

Three Streams are running in the **Stream Tab**:

1. *OpenBCICyton8Channels*: This stream is sent from *MotorImageryBalanceBall.py*. Indicate the EEG data from the OpenBCI Cyton 8 Channels.

2. *EventMarker_BallGame*: This stream is from the Unity platform. Indicate the event marker for the P300 Speller.

3. *MotorImageryInference*: This stream is sent from the *MotorImageryBalanceBall.py* for visualization purpose. Indicate the predicted hand side of imaginary hand movements in evaluation process.


.. figure:: media/balanceBall_StreamTab.png
   :width: 400
   :align: center
   :alt: balance ball stream tab config

.. figure:: media/options_OpenBCICyton8Channels.png
   :width: 400
   :align: center
   :alt: balance ball openbci cyton 8 channels config

.. figure:: media/balanceBall_ScriptingTab.png
   :width: 400
   :align: center
   :alt: balance ball scripting tab config


Set up the Experiment Parameters
************
1. Double-click the Scene *Training* under the path Assets/Scenes/Training.unity
2. In the hierarchy tab, click on *PlayerPlane* GameObject. In its inspector panel, find *Training_PlayerPlane* component attached to the gameoebjct. Here, you can customize the following parameters in the Unity Editor:
        - Max Session Num: The number of times to play animation for each side of hand movements.
        - Break Time: The interval between each animation.

.. figure:: media/balanceBall_Unity_config.png
   :width: 400
   :align: center
   :alt: balance ball unity config

Run the Experiment
************

1. Start by double-clicking the Scene *GameMenu* under the path Assets/Scenes/GameMenu.unity. In this menu page, you can move to the training section by clicking on "TRAIN".

.. figure:: media/balanceBall_Menu.png
   :width: 400
   :align: center
   :alt: balance ball menu

2. Once you click *TRAIN*, the training section is automatically started. Instructions printed on the screen will ask the user to imagine left or right hand movements while looking at an automated ball moving on the screen.

3. After the training section, the training data will be already fitted to a CSP model. To proceed, click *Escape* to pull up the menu, and click *PLAY* to enter the evaluation section.

5. During the evaluation, the participant can imagine either left or right hand movements to control the tilting of the platform. The side of hand movement with the highest probability will be selected as the prediction result (sent from the MotorImageryInference channel).

.. note::
    At any point of the training process, you can press the *Escape* key to enter the training section again. This will start the training again.



