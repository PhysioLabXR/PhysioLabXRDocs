.. include:: .special.rst

PhysioLab\ :sup:`XR`
#####################################################

*Simplifying Physiological and Neuroimaging Experiments*
**********************************************************
+-----------------------------------------------+-----------------------------------------------+-----------------------------------------+--------------------------------------------------+
| .. image:: media/index_page/visualization.png | ..  image:: media/index_page/realtime_dsp.png | .. image:: media/index_page/replay.png  | .. image:: media/index_page/scripting.png        |
|    :width: 200                                |    :width: 200                                |    :width: 200                          |    :width: 200                                   |
|    :target: Visualization.html                |    :target: DSP.html                          |    :target: Replay.html                 |    :target: Scripting.html                       |
+-----------------------------------------------+-----------------------------------------------+-----------------------------------------+--------------------------------------------------+


- built with 95%+ Python for running physiological/neuroscience/human-computer interaction experiments involving EEG, fNIRS, eyetracking, fMRI, cameras, microphones, and more!
- offers real-time visualization, synchronization, recording, and data processing (e.g., to apply filters and run machine learning models).
- supports multiple experiment platforms (screen-based, VR, and AR).
- works on all major operating systems (Windows, MacOS, and Linux).
- easy to install and use: install via pip and run with a single command, download the executable,or run from source.
- designed for both academic researchers and industry practitioners.
- open-source and community driven.

Streaming Data:

- if you are already using `Lab Streaming Layer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_ or `ZeroMQ (ZMQ) <https://zeromq.org/>`_ to stream your data, then you can easily integrate PhysioLab\ :sup:`XR` into your data pipeline.

- you can also create your own data stream via the `Data Stream API <DataStreamAPI.html>`_.


.. _download:

Download
********

Run the executable
------------------

Get the latest executable of PhysioLab\ :sup:`XR` from the `release page <https://github.com/ApocalyVec/PhysioLabXR/releases>`_, that supports **Windows**, **MacOS**, and **Linux**.

Install with pip
----------------

You can also install PhysioLab\ :sup:`XR` with pip, which will install the latest release version of PhysioLab\ :sup:`XR` and all its dependencies.
This is platform-independent and works on Windows, MacOS, and Linux.

PhysioLab\ :sup:`XR` supports Python 3.9, 3.10, and 3.11. Support for 3.12 is coming soon.

If you are using pip, we recommend installing PhysioLab\ :sup:`XR` in a virtual environment. To create a virtual environment, run:

.. code-block:: bash

    python -m venv physiolabxr-env

Then activate the virtual environment:

.. code-block:: bash

    source physiolabxr-env/bin/activate

Install PhysioLab\ :sup:`XR` with:

.. code-block:: bash

    pip install physiolabxr

Once installed, you can run the application with:

.. code-block:: bash

    physiolabxr

Run from source
---------------

Alternatively, you can run the application from its source. Refer to *Run from Source* in the `developer page <Contribute.html#run-from-source>`_ for instructions.

If you encountered problem installing physiolabxr, please re

Get Started with a Simple Example
*********************************

.. _Simple ERP tutorial:

Event-related Potential with EEG
--------------------------------

In this example, we will replay a prerecorded `EEG <https://en.wikipedia.org/wiki/Electroencephalography>`_
experiment of the `visual oddball paradigm <https://en.wikipedia.org/wiki/Oddball_paradigm>`_,
and extract the `event-related potential (ERP) of the P300 response <https://en.wikipedia.org/wiki/Event-related_potential>`_.
Download the example recording `erp-example.p from here (~4 MB) <https://drive.google.com/file/d/1E6wYPBUIpEIQfd5Bm38Z-LkJvao7-5Tx/view?usp=sharing>`_

After launching PhysioLab\ :sup:`XR`, go to the `Replay Tab <Replay.html>`_ and load the example recording. The `streams <DataStreamAPI.html>`_ in the replayed
recording will be automatically added to the `Visualization Tab <Visualization.html>`_. There, you can click on the start buttons
to see the EEG and event marker stream in real-time, synchronized as they were recorded during the experiment.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/erp-example-replay.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

|

.. note::

    If at any time during the replay, it says 'Lost Connection to ...', it means the replay has finished and the replay streams are closed. When this happens, simply restart the replay from the *Replay Tab* by clicking on *Start Replay* again.


The event marker stream has one channel named *DTN* showing what type of stimulus is popping up for the participant. DTN stands
for distractor, target, and novelty as part of the `oddball paradigm <https://en.wikipedia.org/wiki/Oddball_paradigm>`_.
We should see an ERP in the EEG signal when there's a new value in the DTN stream, meaning a new stimulus just popped up.
However, each EEG channel has a different offset imposed by the EEG hardware, as you can see on the vertical scale of the
EEG plot with values going from -8000 to 6000. To bring them to the same level and make the ERP signal visible, we will
apply a high-pass filter to the EEG stream.

|


Adding Filters to the EEG Stream
--------------------------------
To add filters to the EEG stream:

1. Click on the option button of the plot with EEG data (learn more about `plot options here <Visualization.html#plot-options>`_).
2. Under *Select data processor*, choose *ButterworthHighpassFilter*, and click on *Add*, a filter item will appear in the list of data processors.
3. Set the *Cutoff* frequency for highpass filter to be 1 Hz.
4. To add a second filter, select *ButterworthLowpassFilter*, click on *Add*, and set the *Cutoff* frequency to be 60 Hz.
5. To activate the filters, click on the checkbox before the filters.

The bubble before the filter will turn |stream_active|, meaning the filter is currently active. You will now able to zoom in to
see the ERPs in the filtered EEG signals.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video2" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/erp-example-dsp.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Now, say we want to extract the ERP chunks from EEG, perhaps to save them for classification later. We can do this by adding a
custom script.

|

Adding a Custom Script to Extract EEG Signals
----------------------------------------------

We will add a *script* that uses the DTN stream as a trigger to extract the ERP chunks from the EEG stream (learn more about
`the scripting feature here <Scripting.html>`_). To do this:

#. Go to the *scripting tab*, and click on *Add* to create a new script.
#. Click on *Create* (not *Locate*, which is for loading an existing .py script) and choose a file location to save the script. Name the script something like 'ERPExtraction'. Click on *Save*.
#. A template script will open in your system's default editor. Change it to the following code:

    .. code-block:: python

        import numpy as np

        from physiolabxr.scripting.RenaScript import RenaScript
        from physiolabxr.scripting.physio.epochs import get_event_locked_data, buffer_event_locked_data, get_baselined_event_locked_data


        class ERPExtraction(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)

            # Start will be called once when the run button is hit.
            def init(self):
                self.events = (1, 2, 3)  # 1 is distractor, 2 is target, 3 is novelty
                self.tmin = -0.1  # Time before event marker to include in the epoch
                self.tmax = 0.8  # Time after event marker to include in the epoch
                self.baseline_time = 0.1  # Time period since the ERP epoch start to use as baseline
                self.erp_length = int((self.tmax - self.tmin) * 128)  # Length of the ERP epoch in samples
                self.event_locked_data_buffer = {}  # Dictionary to store event-locked data
                self.eeg_channels = self.get_stream_info('Example-BioSemi-Midline', 'ChannelNames')  # List of EEG channels
                self.srate = self.get_stream_info('Example-BioSemi-Midline', 'NominalSamplingRate')  # Sampling rate of the EEG data in Hz

            # loop is called <Run Frequency> times per second
            def loop(self):
                # first check if the inputs are available
                if 'Example-EventMarker' in self.inputs.keys() and 'Example-BioSemi-Midline' in self.inputs.keys():
                    event_locked_data, last_event_time = get_event_locked_data(event_marker=self.inputs['Example-EventMarker'],
                                                                               data=self.inputs['Example-BioSemi-Midline'],
                                                                               events_of_interest=self.events,
                                                                               tmin=self.tmin,
                                                                               tmax=self.tmax,
                                                                               srate=128,
                                                                               return_last_event_time=True, verbose=1)
                    self.inputs.clear_up_to(last_event_time)  # Clear the input buffer up to the last event time to avoid processing duplicate data
                    self.event_locked_data_buffer = buffer_event_locked_data(event_locked_data, self.event_locked_data_buffer)  # Buffer the event-locked data for further processing

                    if len(event_locked_data) > 0:  # if there's new data
                        if self.params['ChannelToPlot'] in self.eeg_channels:  # check if the channel to plot chosen in the params is valid
                            channel_index = self.eeg_channels.index(self.params['ChannelToPlot'])  # Get the index of the chosen EEG channel from the list
                            baselined_data = get_baselined_event_locked_data(self.event_locked_data_buffer, channel_index, self.baseline_time, self.srate)  # Obtain baselined event-locked data for the chosen channel
                            erp_viz_data = np.zeros((self.erp_length, 2))  # Create a visualization data array for ERP

                            # Populate the visualization data with ERP values from different events (if available)
                            if 1 in baselined_data.keys():
                                erp_viz_data[:, 0] = np.mean(baselined_data[1], axis=0) if self.params['PlotAverage'] else baselined_data[1][-1]
                            if 2 in baselined_data.keys():
                                erp_viz_data[:, 1] = np.mean(baselined_data[2], axis=0) if self.params['PlotAverage'] else baselined_data[2][-1]
                            self.outputs['ERPs'] = np.array(erp_viz_data, dtype=np.float32)  # Set the output 'ERPs' with the visualization data
                        else:
                            print(f"Channel {self.params['ChannelToPlot']} not found")

            # cleanup is called when the stop button is hit
            def cleanup(self):
                print('Cleanup function is called')

#. Save the script in the editor and return to the PhysioLab\ :sup:`XR` *scripting tab*. To have our script receive the EEG and event markers,
   we will add them as `inputs <Scripting#Inputs.html>`_. For visualization purposes, we will add an `output <Scripting#Outputs.html>`_
   called 'ERPs' and send the buffered ERPs to it whenever we have a new event marker.

    * In the *inputs pane*, type "Example-EventMarker", hit enter or click add. Then type "Example-BioSemi-Midline", hit enter or click add again. You should see the two inputs added to the list.

    * In the *outputs pane*, type "ERPs". This is the stream name of the output. Hit enter or click add. In the output item that appears, change the number of output channels to 2, because we are plotting target and distractor ERPs separately.

#. We will next add two parameters to control what's being plotted. You can plot the values of the parameters while the script is running to see the effect of the changes.

    * In the *parameters pane*, type *ChannelToPlot*, hit enter or click add. In the data type dropdown, change its data type
      to str (string). Set its value to "FPz". This is the channel from which we will plot the ERPs.
    * Then type *PlotAverage*, hit enter or click add. We will leave the data type as bool (boolean). When it is set to true,
      the data sent to ERP will be the average of all the ERPs of the chosen channel. When it is set to false, the ERPs will
      plot the most recent ERP of the chosen channel.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video3" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/erp-example-scripting1.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>


5. Click on *Run* to run the script. Go back to the *Visualization tab*, type in ERPs and click on *Add* or hit enter.
   You should see a new stream called ERPs added to the plots with |stream_available| at the bottom, meaning this stream is available on the network, and you can start receiving and plotting it.
6. Click on the *Play* button of the ERPs plot widget to start the data flow. You should see the ERPs of the chosen channel
   plotted in the visualization tab. The first channel (red) shows the distractor ERPs and the second channel (blue) shows the targets.

Feel free to play around with the parameters to change the channel being plotted to see the effect of the changes. You can also set the *PlotAverage* parameter
to true to check out the averaged ERPs for the target and distractor events. You can also choose one of the channels as they show up in the EEG plot in the *Visualization tab*.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video4" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/erp-example-scripting2.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Further Information
-------------------

Where can you take it from here? Check out :ref:`the tutorial on building a P300 speller game <tutorial p300>`, where you can spell words
through a user interface enabled by the classification of ERP signals.

OR

When you have an eyetracker, the pupil size it captures can be a helpful feature for classifying
ERPs (more info `here <https://onlinelibrary.wiley.com/doi/full/10.1111/psyp.12378>`_). Take a look at
`this guide <tutorials/BuildMultiModalClassifier.html>`_ on
how to build a multimodal classifier with PhysioLab\ :sup:`XR`, and `this one <FixationDetection.html>`_ on how to create a real-time fixation detection algorithm
to know where exactly the user's gaze is focused.


.. toctree::
    :maxdepth: 1
    :caption: Features:

    Visualization
    Recording
    Replay
    Scripting
    ScriptingAPIReference
    DSP
    ViewInMatlab

.. toctree::
    :maxdepth: 1
    :caption: Stream data:

    DataStreamAPI
    DataStreamAPI/ZMQStream
    DataStreamAPI/LSLStream
    DataStreamAPI/ScriptStream
    DataStreamAPI/StreamVideoAudioScreenCapture

.. toctree::
    :maxdepth: 1
    :caption: Tutorials:

    FixationDetection
    PsychoPy
    tutorials/BuildMultiModalERPClassifier
    PhysioLabXRP300SpellerDemo
    ObjectDetectionTutorial

.. toctree::
    :maxdepth: 1
    :caption: Developers:

    BuildGuide
    Contribute

.. toctree::
    :maxdepth: 1
    :caption: Others:

    Benchmarks
    Releases
    LICENSE


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
            checkAndPlayVideo("autoplay-video1");
            checkAndPlayVideo("autoplay-video2");
            checkAndPlayVideo("autoplay-video3");
            checkAndPlayVideo("autoplay-video4");
            // Add more videos as needed, using their respective video IDs
        });
    </script>


.. |stream_available| image:: /media/streamwidget_stream_available.svg
   :width: 20px
   :height: 20px

.. |stream_active| image:: /media/streamwidget_stream_viz_active.svg
   :width: 20px
   :height: 20px
