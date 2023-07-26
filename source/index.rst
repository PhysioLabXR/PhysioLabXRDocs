Welcome to PhysioLab\ :sup:`XR` documentation
=================================================

*physiological and neuroimaging experiments, made easy*
*******************************************************
+-----------------------------------------------+-----------------------------------------------+-----------------------------------------+--------------------------------------------------+
| .. image:: media/index_page/visualization.png | ..  image:: media/index_page/realtime_dsp.png | .. image:: media/index_page/replay.png  | .. image:: media/index_page/scripting.png        |
|    :width: 200                                |    :width: 200                                |    :width: 200                          |    :width: 200                                   |
|    :target: Visualization.html                |    :target: DSP.html                          |    :target: Replay.html                 |    :target: Scripting.html                       |
+-----------------------------------------------+-----------------------------------------------+-----------------------------------------+--------------------------------------------------+


PhysioLab\ :sup:`XR` is a pure Python application for running physiological experiment involving EEG, fNIRS, eyetracking, fMRI,
audiovisual recording from cameras and microphones, and more!
Tailored for academic researchers and industry practitioners, it supports multiple platforms (screen-based, VR, or AR)
and offers real-time visualization, synchronization, recording, and data processing (i.e., apply filters and run machine
learning models).

You may easily integrate RenaLabApp into your data pipeline if
they are already using `Lab Streaming Layer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_ or `ZMQ <https://zeromq.org/>`_
to stream your data. Otherwise you are welcome to create your own data stream via the `Data Stream API <DataStreamAPI.html>`_.

Download
========
Get the latest release of PhysioLab\ :sup:`XR` `here <https://github.com/ApocalyVec/PhysioLabXR/releases>`_.
For the stable version, use `v0.1.0-beta <https://github.com/ApocalyVec/PhysioLabXR/tree/refs/tags/v0.1.0-beta>`_. The packaged release supports Windows, MacOS, and Linux.

Alternatively, you can run the application from the source. Refer to *Run from Source* in the `developer page <Developer.html>`_ for instructions.

Get Started with a Simple Example
================================

Event-related Potential with EEG
--------------------------------
We will replay a pre-recorded `EEG <https://en.wikipedia.org/wiki/Electroencephalography>`_
experiment of the `visual oddball paradigm <https://en.wikipedia.org/wiki/Oddball_paradigm>`_,
and extract the `event-related potential (ERP) of the P300 response <https://en.wikipedia.org/wiki/Event-related_potential>`_.
Download the example recording `erp-example.p from here (~4 MB) <https://drive.google.com/file/d/12xR1bbFF3oc9XVN6UVHGvazcJ5ol9Y7u/view?usp=sharing>`_

On launch PhysioLab\ :sup:`XR`, go to the `Replay Tab <Replay.html>`_ and load the example recording. The streams in the replayed
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

The event marker stream has one channel named *DTN* showing what type of stimulus is popping up for the participant. DTN stands
for distractor, target, and novelty as part of the `oddball paradigm <https://en.wikipedia.org/wiki/Oddball_paradigm>`_.
We should see an ERP in the EEG signal when there's a new value in the DTN stream, meaning a new stimulus just popped up.
However, each EEG channel is on a different offset imposed by the EEG hardware. to make the ERP signal visible, we will
apply a high-pass filter to the EEG stream.

|
_______________________________________________________________________________________________________________________

To add a filter to the EEG stream:

1. Click on the option button of the plot with EEG data (learn more about `plot options here <Visualization.html#plot-options>`_).
2. Under *Select data processor*, choose *ButterworthHighpassFilter*, and click on *Add*, a filter item will appear in the list of data processors.
3. Set the *Cutoff* frequency for highpass filter to be 1 Hz. Then click on the checkbox before the filter item to activate the filter.

The bubble before the filter will turn green, meaning the filter is currently active. You will now about to zoom in to see
see the ERPs in the filtered EEG signals.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video2" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/erp-example-dsp.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Now when some downstream task wants to use the ERP as a feature for classification, we want to extract the ERP chunks from the EEG stream.

|
_______________________________________________________________________________________________________________________

To do this, we will add a *script* that use the DTN stream as a trigger to extract the ERP chunks from the EEG stream.

A more detailed tutorial on P300

building a multimodal classifier

Hand digits classification
--------------------------





Further Information
-------------------
.. toctree::
   :maxdepth: 1
   :caption: Contents:

   Recording
   Replay
   Scripting
   SupportedSensors
   Developer
   Stream
   DataStreamAPI
   ViewInMatlab
   PsychoPy
   BuildGuide
   ContributeToThisDoc
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
            // Add more videos as needed, using their respective video IDs
        });
    </script>