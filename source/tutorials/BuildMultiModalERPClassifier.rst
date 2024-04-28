.. _tutorial multi-modal erp Classifier:

##################################################################
Building a multi-modal ERP Classifier based on EEG and Pupil Size
##################################################################

In this tutorial, we will build a classifier that uses `event-related potential (ERP) <https://en.wikipedia.org/wiki/Event-related_potential>`_ signal found in both
EEG and pupillometry (i.e., pupil size from eyetracker) for a `visual oddball task <https://en.wikipedia.org/wiki/Oddball_paradigm>`_. The classifier
will be able to tell if the participant sees a distractor or a target object that they are instructed to look for.

.. raw:: html

    <figure>
        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="../_static/multimodal-teaser.mp4" type="video/mp4">
            </video>
        </div>

        <figcaption style="margin-top: 10px;">
            The complete pipeline you will build in this tutorial. In the middle, a video shows what the participant sees. Target and distractor objects pop up one by one in front of the participant and they need to count the number of targets. On the right, you can see the EEG, eye data and event marker streams. They are used to build the multi-modal classifier, which predicts if the participant sees a target or a distractor object in real time.
        </figcaption>
    </figure>


As always, the data from the experiment is available for download. We will use the :ref:`replay feature<feature replay>` in PhysioLab\ :sup:`XR` to replay the data,
and using the :ref:`scripting feature<feature scripting>`, build a classifier that trains and make predictions in real-time as the participant performing the task.
You can take this tutorial as a starting point to build your own pipeline involving real-time machine learning.

.. note::

    This tutorial assumes that you are familiar with the basics of PhysioLab\ :sup:`XR`. We recommend you to go through
    :ref:`the simple ERP tutorial with EEG <Simple ERP tutorial>` before trying this one.


.. _multimodal data download:

Data in this experiment
************************

Download the recording from `here (~5.3 GB) <https://drive.google.com/file/d/1nn0UZoY6OlwiDhkNM25QfXr9tWa_HTkN/view?usp=sharing>`_.
Here's how the data looks like when replayed.

.. note::

    Check out the :ref:`replay page<feature replay>` for more information on how to replay the data. This replay includes
    image data, you can learn how to visualize image data in this :ref:`section<visualization-image>`.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/multimodal-classifier-replay.mp4" type="video/mp4">
        </video>
    </div>

Similar to the :ref:`simple ERP tutorial <Simple ERP tutorial>`, this experiment is a visual oddball task.
Participant is instructed to look for a target object (e.g., an red apple) among distractor objects (e.g., other fruits).
Different objects are presented to the
the participant, who needs to count the number of times that a target appears. Each appearance of
an object is called a *trial*. Multiple trials are grouped into a *block*, with a short break in between blocks.
Each block contains 30 trials, and there
are a total of 9 blocks. Our goal is to build a classifier that can tell if a trial is a target or a distractor trial.

There are 5 streams in this recording:

- Example-BioSemi-64Chan: 64 channels EEG collected from `BioSemi Active 2 headset <https://www.biosemi.com/products.htm>`_,
  downsampled from 2048 Hz to 128 Hz to reduce file size. This is one of the inputs to our classifier.

- Example-Eyetracking-Pupil: 6 channels eyetracking data from the built-in eyetracker in Varjo XR-3 headset. First 3 channels
  are the 3D gaze direction, the 4th channel is the gaze status, where a value of 2 means eyes are tracked and 0 means
  eyes are not tracked. The last 2 channels are the left and right pupil sizes; they are the second input to our classifier.

- Example-Video: the 400 by 400 screen capture from the stimulus presentation software (`Unity <https://unity.com/>`_ in this case). This is not used in the classifier but
  we will use it for visualization.

- Example-EventMarker-DTN-Block: event marker stream from the stimulus presentation. It has two
  channels. This first is called DTN (with values 1, 2, and 3 for distractor, target, and novelty, respectively). This channel
  will be our labels for the trials. We will use these labels when training the classifier. The second channel is
  BlockID. It has integer values ranging 1 to 9, and each corresponds to a block of trials. A position value indicates
  the start of a block and a negative value indicates the end of a block. For example, if we receive a value of 1, it
  means that the first block is starting. If we receive a value of -1, it means that the first block just ended.
  *We will use this information to split the blocks and only train the model in between blocks, and make predicts during a
  block*.


Settings things up
*******************

Because we will use the built-in real-time filter to process the EEG data, we need to do the following

1. The stream needs to start as soon as the replay starts. To do this, go to the ``File->Settings->Replay``,
   and check the box that says ``Start the streams when the replay starts``. This way we can make sure we capture
   all the data from the beginning of the replay.

2. The filter need to be turned on before the replay starts. To do this,

   1. go to the ``Stream`` tab, add the stream 'Example-BioSemi-64Chan', click ``Add`` or hit the *Enter* key to add the stream.

   2. click the |options| button for the newly added stream, select the group ``default group name``

   3. From the ``Select data processor`` dropdown, choose ``NotchFilter``, and click ``Add``. Set the `freq` to *60*.

   4. choose ``ButterworthLowpassFilter``, and click ``Add``. Set the ``Cutoff`` to *30*.

   5. choose ``ButterworthHighpassFilter``, and click ``Add``. Set the ``Cutoff`` to *0.1*.

   6. Enable the filters by click the checkbox next to the filter names. You are good to go if you see a green bubble like this |filter_active| at the left side of all three filters.

3. For better visualization of the filtered EEG data, we add a constant offset to the each channels in the EEG stream. To do this,
   in the same window where you added the filters. Change the value for ``Group Channels Constant Offset`` to *50*. This will
   add a constant offset of 50 to each channel in the EEG stream. This only affect the visualiation and not the actual data.

Here's the video of how to add the filters and change the ``Group Channels Constant Offset``.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/multimodal-add-filter-change-offset.mp4" type="video/mp4">
        </video>
    </div>

Add and run the script
***********************

Before we get further, some terminology:

.. _about epochs:
.. note::

        **What is an Epoch:** In neuroscience lingo, an *epoch* is a segment of data that is time-locked to an event. Time-locking means that
        the data is aligned to the event, so that the event happens at a specific time point in the epoch. For example,
        we want to find what people's EEG looks like when they see a target object. We will time-lock the EEG data to the
        event when the target object appears.
        We assume the response to the event happens between -0.1 to 0.8 seconds before and after seeing the target,
        so we will take a segment of data before and after the event. We have a negative 0.1 second because we want to
        see if there's any anticipation of the event before it happens.
        Say our EEG is has 64 channels and sampled at 128 Hz. Taking a segment of data from -0.1 to 0.8 seconds means we will take 128 * (0.8 + 0.1) = 115 time points per epoch.
        If we have 100 target trials, we will have 100 epochs, each with 64 channels and 115 time points. The shape of the
        data array will be (100, 64, 115).

Now you understand what an epoch is, let's get back to the tutorial. We will use the :ref:`scripting feature<feature scripting>` to build the classifier.
The script that we will add is called ``ERPClassifier.py``. Here it is:

.. code-block:: python

    import os

    import cv2
    import numpy as np
    import scipy
    from sklearn.model_selection import StratifiedShuffleSplit
    from sklearn.metrics import classification_report

    from physiolabxr.scripting.RenaScript import RenaScript
    from physiolabxr.scripting.events.utils import get_indices_when
    from physiolabxr.scripting.physio.HDCA import HDCA
    from physiolabxr.scripting.physio.epochs import get_event_locked_data, buffer_event_locked_data, \
        get_baselined_event_locked_data, visualize_epochs
    from physiolabxr.scripting.physio.interpolation import interpolate_zeros
    from physiolabxr.scripting.physio.preprocess import preprocess_samples_eeg_pupil
    from physiolabxr.scripting.physio.utils import rebalance_classes
    from physiolabxr.utils.data_utils import get_date_string


    class ERPClassifier(RenaScript):
        def __init__(self, *args, **kwargs):
            """
            Please do not edit this function
            """
            super().__init__(*args, **kwargs)

        # Start will be called once when the run button is hit.
        def init(self):
            self.dtn_events = (1, 2)  # 1 is distractor, 2 is target

            self.event_marker_name = 'Example-EventMarker-DTN-Block'
            self.video_output_name = 'Example-Video-Output'
            self.video_input_name = 'Example-Video'
            self.video_shape = (400, 400, 3)  # the shape of the video stream, because all inputs are flattened as they comes in, we need to reshape the video frames to be able to put shapes on them
            self.last_dtn_time = 0

            self.eeg_channels = self.get_stream_info('Example-BioSemi-64Chan', 'ChannelNames')  # List of EEG channels
            self.eeg_srate = self.get_stream_info('Example-BioSemi-64Chan', 'NominalSamplingRate')  # Sampling rate of the EEG data in Hz
            self.tmin_eeg = -0.1  # Time before event marker to include in the epoch
            self.tmax_eeg = 0.8  # Time after event marker to include in the epoch

            self.eeg_baseline_time = 0.1  # Time period since the ERP epoch start to use as baseline
            self.eeg_erp_length = int((self.tmin_eeg - self.tmax_eeg) * self.eeg_srate)  # Length of the ERP epoch in samples

            self.eeg_midlines = ['Fpz', 'AFz', 'Fz', 'FCz', 'Cz', 'CPz', 'Pz', 'POz', 'Oz', 'Iz']  # List of midline EEG channels
            self.eeg_picks = {self.eeg_channels.index(channel): channel for channel in self.eeg_midlines}  # Indices of the midline EEG channels, used for visualization

            self.eye_channels = self.get_stream_info('Example-Eyetracking-Pupil', 'ChannelNames')  # List of eye tracking channels
            self.eye_srate = self.get_stream_info('Example-Eyetracking-Pupil', 'NominalSamplingRate')  # Sampling rate of the eye tracking data in Hz

            self.tmin_eye = -0.5  # Time before event marker to include in the epoch
            self.tmax_eye = 3.  # Time after event marker to include in the epoch
            self.eye_baseline_time = 0.5  # Time period since the ERP epoch start to use as baseline
            self.eye_erp_length = int((self.tmin_eye - self.tmax_eye) * self.eye_srate)  # Length of the ERP epoch in samples

            self.event_locked_data_buffer = {}  # Dictionary to store event-locked data
            self.process_next_look = False
            self.block_end_time = None
            self.block_count = 0

            self.random_seed = 42

            self.model = None
            self.eeg_pca = None
            self.eeg_ica = None
            self.event_indicators = []
            self.indicator_radius = int(0.5 * 0.9 * self.video_shape[0] // 30)  # 0.5 for the radius, 0.9 for the margins, 30 for the number items per block
            self.indicator_separation = int(1.1 * self.video_shape[0] // 30)
            self.horizontal_offset = 5

            # create a 'figure' folder to save the figures generated by the script
            # get a date string to be used as the folder name
            self.figure_folder = os.path.join('figures', get_date_string())
            os.makedirs(self.figure_folder, exist_ok=True)
            print(f"Figures will be saved to {self.figure_folder}")

        def loop(self):
            # wait until we have received the last pupil block
            # the following line gets the last pupil time, this is used to check if we have received the last pupil epoch, which is 3 seconds after the event
            last_pupil_time = self.inputs['Example-Eyetracking-Pupil'][1][-1] if 'Example-Eyetracking-Pupil' in self.inputs.keys() else 0
            if self.process_next_look and last_pupil_time - 3 > self.block_end_time:
                self.process_next_look = False
                self.block_count += 1

                last_event_time, baselined_eeg_epochs, baselined_resampled_pupil_epochs, x_eeg_znormed, x_eeg_pca_ica, x_pupil_znormed, y\
                    = self.process_data(self.inputs[self.event_marker_name], is_rebalance=True)

                self.inputs.clear_up_to(self.block_end_time)  # Clear the input buffer up to the last event time to avoid processing duplicate data

                visualize_epochs(baselined_eeg_epochs, picks=self.eeg_picks, tmin=self.tmin_eeg, tmax=self.tmax_eeg, title=f'EEG Epochs, block {self.block_count}', out_dir=self.figure_folder)
                visualize_epochs(baselined_resampled_pupil_epochs, tmin=self.tmin_eye, tmax=self.tmax_eye, title=f'Pupil Size Epochs, block {self.block_count}', out_dir=self.figure_folder)

                # split the data into train and test, we only do one split as we are not doing cross validation here
                skf = StratifiedShuffleSplit(n_splits=1, random_state=self.random_seed, test_size=0.2)
                train, test = [(train, test) for train, test in skf.split(x_eeg_znormed, y)][0]
                x_eeg_znormed_train, x_eeg_pca_ica_train = x_eeg_znormed[train], x_eeg_pca_ica[train]
                x_eeg_znormed_test, x_eeg_pca_ica_test = x_eeg_znormed[test], x_eeg_pca_ica[test]
                x_pupil_train, x_pupil_test = x_pupil_znormed[train], x_pupil_znormed[test]
                y_train, y_test = y[train], y[test]

                target_names = ['distractor', 'target']

                # hdca classifier
                self.model = HDCA(target_names)
                roc_auc_combined_train, roc_auc_eeg_train, roc_auc_pupil_train = self.model.fit(x_eeg_znormed_train, x_eeg_pca_ica_train, x_pupil_train, y_train, num_folds=1, is_plots=True, exg_srate=self.eeg_srate, notes=f"Block ID {self.block_count}", verbose=0, random_seed=self.random_seed, out_dir=self.figure_folder)  # give the original eeg data, no need to apply HDCA again
                y_pred_train, roc_auc_eeg_pupil_test, roc_auc_eeg_test, roc_auc_pupil_test = self.model.eval(x_eeg_znormed_train, x_eeg_pca_ica_train, x_pupil_train, y_train, notes=f"Block ID {self.block_count}")
                y_pred_test, roc_auc_eeg_pupil_test, roc_auc_eeg_test, roc_auc_pupil_test = self.model.eval(x_eeg_znormed_test, x_eeg_pca_ica_test, x_pupil_test, y_test, notes=f"Block ID {self.block_count}")

                # report the results
                print(f"Block ID {self.block_count}:\n")
                print('Train performance: \n' + classification_report(y_train, y_pred_train, target_names=target_names))
                print(f"combined ROC {roc_auc_combined_train}, ROC EEG {roc_auc_eeg_train}, ROC pupil {roc_auc_pupil_train}")

                print('Test performance: \n' + classification_report(y_test, y_pred_test, target_names=target_names))
                print(f"combined ROC {roc_auc_eeg_pupil_test}, ROC EEG {roc_auc_eeg_test}, ROC pupil {roc_auc_pupil_test}")

            if self.event_marker_name in self.inputs.keys():
                event_markers = self.inputs[self.event_marker_name][0]
                event_times = self.inputs[self.event_marker_name][1]

                block_ids = event_markers[1, :]
                if (block_end_indices := get_indices_when(block_ids, lambda x: x < 0)) is not None:  # if we received the block end event, which is a negative block ID
                    self.block_end_time = event_times[block_end_indices[-1]]
                    self.process_next_look = True
                    # clear the event and prediction indicators at the end of each block
                    self.event_indicators = []
                    self.prediction_indicators = []

                # check if any DTN (distractor, target, or novelty) event is received
                dtns = event_markers[0, :][event_times > self.last_dtn_time]
                dtns_times = event_times[event_times > self.last_dtn_time]
                dtn_indices, found_dtns = get_indices_when(dtns, lambda x: np.isin(x, self.dtn_events), return_data=True)
                if dtn_indices is not None:  # if we received the block end event, which is a negative block ID
                    self.last_dtn_time = dtns_times[dtn_indices[-1]]
                    # add the dtn events to the event indicators, so they can be visualized on the video
                    new_dtn_events = [{'time': t, 'type': event_type} for t, event_type in zip(dtns_times, found_dtns[:, 0])]
                    self.event_indicators += new_dtn_events

                    # check if any dtn has its pupil epoch ready (3 seconds has elapsed since the event), if so, make a prediction
                    if self.model is not None:
                        for event in self.event_indicators:
                            # if this event have not been predicted yet
                            if last_pupil_time - 3 > event['time'] and 'pred' not in event:
                                this_event_marker = (np.array([[event['type']]]), np.array([event['time']]))  # event needs to be a 2D array as in [[event['type']]]
                                _, _, _, _, x_eeg_pca_ica, x_pupil_znormed, _ = self.process_data(this_event_marker, is_rebalance=False, use_buffer_data=False, use_previous_pca_ica=True, verbose=None)
                                y_pred = self.model.transform(x_eeg_pca_ica, x_pupil_znormed)
                                event['pred'] = y_pred[0]

            if self.video_input_name in self.inputs.keys():
                video_frame = self.inputs[self.video_input_name][0][:, -1].reshape(self.video_shape).copy()  # get the video frames
                if len(self.event_indicators) > 0:
                    # draw event indicator as blue (distractor) and red (target) on the bottom part of the video frame
                    for i, event in enumerate(self.event_indicators):
                        cv2.circle(video_frame, (self.video_shape[0] - self.indicator_separation, self.horizontal_offset + i * self.indicator_separation), self.indicator_radius, (0, 0, 255) if event['type'] == 2 else (255, 0, 0), 2)
                        if 'pred' in event:
                            # 1 is target in the model
                            cv2.circle(video_frame, (self.video_shape[0] - self.indicator_separation * 2, self.horizontal_offset + i * self.indicator_separation), self.indicator_radius, (0, 0, 235) if event['pred'] == 1 else (235, 0, 0), 2)
                # send the video frame to the output
                self.outputs[self.video_output_name] = video_frame.reshape(-1)

        def process_data(self, event_markers, use_buffer_data=True, is_rebalance=True, use_previous_pca_ica=False, verbose=True):

            # we need to interpolate the pupil data to remove the blinks when pupil size will be zeroes
            left_pupil = self.inputs['Example-Eyetracking-Pupil'][0][self.eye_channels.index("Left Pupil Size")]
            right_pupil = self.inputs['Example-Eyetracking-Pupil'][0][self.eye_channels.index("Right Pupil Size")]
            self.inputs['Example-Eyetracking-Pupil'][0][self.eye_channels.index("Left Pupil Size")] = interpolate_zeros(left_pupil)
            self.inputs['Example-Eyetracking-Pupil'][0][self.eye_channels.index("Right Pupil Size")] = interpolate_zeros(right_pupil)

            event_locked_data, last_event_time = get_event_locked_data(event_marker=event_markers,
                                                           data={'eeg': self.inputs['Example-BioSemi-64Chan'],
                                                                 'eye': self.inputs['Example-Eyetracking-Pupil']},
                                                           events_of_interest=self.dtn_events,
                                                           tmin={'eeg': self.tmin_eeg, 'eye': self.tmin_eye},
                                                           tmax={'eeg': self.tmax_eeg, 'eye': self.tmax_eye},
                                                           srate={'eeg': self.eeg_srate, 'eye': self.eye_srate},
                                                           return_last_event_time=True, verbose=verbose)
            if use_buffer_data:
                self.event_locked_data_buffer = buffer_event_locked_data(event_locked_data, self.event_locked_data_buffer)  # Buffer the event-locked data for further processing
                event_locked_data = self.event_locked_data_buffer

            eeg_epochs = {event: data['eeg'] for event, data in event_locked_data.items() if 'eeg' in data}
            baselined_eeg_epochs = get_baselined_event_locked_data(eeg_epochs, self.eeg_baseline_time, self.eeg_srate)  # Obtain baselined event-locked data for the chosen channel
            pupil_epochs = {event: data['eye'][:, 4:] for event, data in event_locked_data.items() if 'eye' in data}
            baselined_pupil_epochs = get_baselined_event_locked_data(pupil_epochs, self.eye_baseline_time, self.eye_srate)
            baselined_resampled_pupil_epochs = {e: scipy.signal.resample(x, x.shape[-1] // 10, axis=-1) for e, x in baselined_pupil_epochs.items()}

            y = np.concatenate([np.ones(x['eeg'].shape[0]) * e for e, x in event_locked_data.items() if 'eeg' in x], axis=0)
            # adjust the labels's value to from 1, 2 to 0, 1
            y = y - 1
            if verbose: print(f"target: {np.sum(y)}. distractor {np.sum(y == 0)}. target ratio: {np.sum(y == 1) / len(y)}")
            x_eeg = np.concatenate([x for e, x in baselined_eeg_epochs.items()], axis=0)
            x_pupil = np.concatenate([x for e, x in baselined_resampled_pupil_epochs.items()], axis=0)

            if is_rebalance:  # rebalance the dataset, this is used in training, not in inference
                x_eeg, y_eeg = rebalance_classes(x_eeg, y, by_channel=True, random_seed=42)
                x_pupil, y_pupil = rebalance_classes(x_pupil, y, by_channel=True, random_seed=42)
                assert np.all(y_eeg == y_pupil)
                y = y_eeg

            # preprocess the data, compute pca and ica for eeg
            if not use_previous_pca_ica:
                x_eeg_znormed, x_eeg_pca_ica, x_pupil_znormed, self.eeg_pca, self.eeg_ica = preprocess_samples_eeg_pupil(x_eeg, x_pupil,20)
            else:
                x_eeg_znormed, x_eeg_pca_ica, x_pupil_znormed, _, _ = preprocess_samples_eeg_pupil(x_eeg, x_pupil, 20, pca=self.eeg_pca, ica=self.eeg_ica)

            return last_event_time, baselined_eeg_epochs, baselined_resampled_pupil_epochs, x_eeg_znormed, x_eeg_pca_ica, x_pupil_znormed, y

        def cleanup(self):
            print('Cleanup function is called')



The script does the following. Don't worry if you don't understand some parts, more details will be explained in the later sections.

1. Extract the EEG and pupil epochs at the end of each block, and :ref:`plot them <figures generated in the pipeline>`.

2. Train a multi-modal :ref:`classifier <multimodal classifier>` to predict target vs. distractor epochs.
   Plot the cross-window weights for the EEG and pupil epochs. Print the classification report and ROC AUC score in the console.

3. After the first block (a classifier is trained), make predictions in real-time during the blocks.
   The predictions are visualized on the video stream named "Example-Video-Output".

Now to use this script in our pipeline, we need to add it, change its ``run frequency``, connect ``inputs`` and ``outputs``.
Here's how to do it:

1. go to the ``Script Tab``. Click the ``Add`` button.

2. Click the ``Create`` button. This will create a new script. Copy and paste the script above to the editor. Alternatively,
   you can save the above script in to a .py file and click the ``Locate`` button to load the script.

3. Change the ``Run Frequency`` to ``30``. This means that the script will run at most 30 times per second. This is because
   we want to make real-time predictions during a block.

4. We will connect four inputs. In the ``Input`` dropdown, add the following

   - "Example-BioSemi-64Chan"

   - "Example-Eyetracking-Pupil"

   - "Example-EventMarker-DTN-Block"

   - "Example-Video"

5. We will connect one output. In the ``Output`` dropdown, add "Example-Video-Output" to the output. Change the number of
   channels to 480000 (400 width * 400 height * 3 channels). Change the stream interface to ``ZMQ``, and copy the port number
   (default 11000 as this is the first output added to the script). We need the port number later to connect this output
   stream in the ``Stream`` tab.


Click the ``Run`` button to start the script, you should see the following in the console:


.. code-block::

    RenaScript: Script init completed
    Base start function is called
    Figures will be saved to figures\<some date and time>
    Entering loop

Here's a video showing the steps above:

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/multimodal-add-script.mp4" type="video/mp4">
        </video>
    </div>

Replay the data
****************

Now the script is running, we can supply the data by replaying it. Go to the ``Replay`` tab, click the ``Browse`` button.
In the file browser, select the ``.p`` file that you :ref:`downloaded <multimodal data download>`. Click the ``Start Replay``
button to start replaying the data.

Here's a video showing how to start the replay:

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/multimodal-start-replay.mp4" type="video/mp4">
        </video>
    </div>

Visualize the real-time predictions
************************************

As a final step, we need to connect to the output stream of the script to visualize the real-time predictions.
To do so, do the following

1. go back to the ``Stream`` tab and add "Example-Video-Output" as a new stream, change the stream type to ``ZMQ``,
   and set the port number to the output's port number from the script. Click the ``Add`` button to add the stream.
   Click the |start| button to start the stream. It will ask you if you want to set the number channels from the default
   1 to 480000. Click ``Yes``.

2. this is a video stream, we need to let PhysioLab\ :sup:`XR` know its parameters such as its resolution. Click
   the |options| button for the newly added stream, select the group ``default group name``. Set the ``Width`` to *400*,
   ``Height`` to *400*, and ``Image`` to *bgr*. The text under these parameters should turn green, and the video
   should show up. You may need to click the [A] button in the video window to resize the video to fit the window.

Now our pipeline is complete. To recap, here's a video showing all the steps above from start to finish, including visualization of the real-time predictions:

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/multimodal-complete.mp4" type="video/mp4">
        </video>
    </div>


.. note::

    **Lags in prediction:** You will see the real-time prediction lags behind the actual event by a bit. This is because the pupil epochs are 3 seconds
    long (see this `paper <https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0091321>`_ for why we use 3 seconds for pupil epochs), compare
    to the EEG epochs, which are 0.9 seconds long. The classifier needs to wait until a complete pupil epoch is collected after an event.
    Therefore, the prediction will be delayed by 3 seconds after the event.

.. _figures generated in the pipeline:

Figures generated in the pipeline
**********************************

The script generates figures for the EEG and pupil epochs at the end of each block. The figures are saved to the
``figure/<date and time>`` folder in the 'app's root directory'. If you don't know where to find it, you can
check the console output of the script for the string "Figures will be saved to ...".
This line will be printed as the last step of the script's :code:`init`. Here's how the EEG and pupil epochs look like at the last block.
You can see the target response in the EEG epochs, which peaks at around 300-400 ms after the target appears.
The pupil size is also larger for the target trials, peaking at around 1500 ms seconds after the target appears.

+-----------------------------------------------------------+--------------------------------------------------------------+
| .. image:: ../media/multimodal_eeg_epochs_final_block.png | .. image:: ../media/multimodal_pupil_epochs_final_block.png  |
|    :width: 400                                            |    :width: 400                                               |
+-----------------------------------------------------------+--------------------------------------------------------------+

The raw epochs has plenty of noise and artifacts, which can make the figure hard to interpret. To help us see the
what time window has the most differentiable signal between target and distractor trials, we will use the
Hierarchical Discriminant Component Analysis (HDCA) classifier to generate cross-bin weights for the EEG and pupil epochs.
This is the topic for the next section.


.. _multimodal classifier:

The HDCA classifier
********************

The classifier we use for this tutorial is called `Hierarchical Discriminant Component Analysis <https://ieeexplore.ieee.org/abstract/document/1029219>`_.
It is one of the more interpretable classifiers, and it is also very fast. It is a two-stage classifier. The first stage computes
the fisher discriminant components across the channels. The second stage takes the discriminate components from EEG and pupil,
concatenate them, and use them to build a linear logistic regression model to classify the target vs. distractor trials.

You can read more about the classifier in `this paper <https://ieeexplore.ieee.org/abstract/document/1029219>`_.

The HDCA classifier, when trained, generates *cross-window weights* that shows how much each time window contributes
to the classification.

In our example, the script generates the cross-window weights for the EEG and pupil epochs at the end of each block.

Here's the cross-window weights for the EEG and pupil at the last block:

+--------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
| .. image:: ../media/multimodal_eeg_classifier_crossbin_weights_final_block.png | .. image:: ../media/multimodal_pupil_classifier_crossbin_weights_final_block.png  |
|    :width: 400                                                                 |    :width: 400                                                                    |
+--------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+

You will see the EEG window that contribute the most to the classification is 300-400 ms after the target appears.
For the pupil, the window that contribute the most is between 1000ms to 1500 ms after the target appears.

Further Information
*******************

Now you know how to build a multi-modal classifier and make predictions in real-time using PhysioLab\ :sup:`XR`.
You can use this tutorial as a starting point to build your own pipeline!
Don't hesitate to contact us if you have any questions or suggestions for this tutorial.


.. raw:: html

    <script>
        // Function to check if a video is visible in the viewport
        function isVideoVisible(videoId) {
            var video = document.getElementById(videoId);
            var rect = video.getBoundingClientRect();
            return rect.top >= 0 && rect.bottom <= window.innerHeight;
        }

        // Function to start the video if it is visible
        function checkAndPlayVideo(videoId) {
            var video = document.getElementById(videoId);
            if (isVideoVisible(videoId) && video.paused) {
                video.play();
            }
        }

        // Attach an event listener to check when a video is in the viewport
        window.addEventListener("scroll", function() {
            checkAndPlayVideo("autoplay-video-teaser");
            checkAndPlayVideo("autoplay-video-replay");
            checkAndPlayVideo("autoplay-video-gap-fill");
            checkAndPlayVideo("autoplay-video-fd");
            checkAndPlayVideo("autoplay-video-video");
            // Add more videos as needed, using their respective video IDs
        });
    </script>


.. |options| image:: /media/options.svg
   :width: 20px
   :height: 20px

.. |filter_active| image:: /media/streamwidget_stream_viz_active.svg
   :width: 20px
   :height: 20px

.. |start| image:: /media/start.svg
   :width: 20px
   :height: 20px