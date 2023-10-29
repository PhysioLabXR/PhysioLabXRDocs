.. _tutorial multi-modal erp Classifier:

#####################################################
Building ERP Classifier based on EEG and Pupil Size
#####################################################



In this tutorial, we will build a classifier that uses `event-related potential (ERP) <https://en.wikipedia.org/wiki/Event-related_potential>`_ signal found in both
EEG and pupil size for a `visual oddball task <https://en.wikipedia.org/wiki/Oddball_paradigm>`_. The classifier
will be able to tell if the participant is looking at target object that they are instructed to look for, or a distractor.

This tutorial assumes you are familiar with the basic operation of PhysioLab\ :sup:`XR`. We recommend you to go through
:ref:`the simple ERP tutorial with EEG <Simple ERP tutorial>` before trying this one.


Data in this experiment
************************

Download the recording from `here (~4.5 GB) <https://drive.google.com/file/d/1MDPxC77SIx5UG5JK0PUslDZ92Eqv21sD/view?usp=sharing>`_.
Here's how the data looks like when replayed (check out the `fixation detection tutorial <tutorial fixation detection replay>` for how to
replay the data).

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-replay" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/mm_cls_example-replay.mp4" type="video/mp4">
        </video>
    </div>

There are 5 streams in this recording:

- Example-BioSemi-64Chan: 64 channels EEG collected from BioSemi Active 2 headset, already downsampled from 2048 Hz to 128 Hz to reduce file size. This is one of the inputs to our classifier.

- Example-Eyetracking: 6 channels eyetracking data from the built-in eyetracker in Varjo XR-3 headset. First 3 channels
  are the 3D gaze direction, the 4th channel is the gaze status, where a value of 2 means eyes are tracked and 0 means
  eyes are not tracked. The last 2 channels are the left and right pupil sizes; they are the second input to our classifier.

- Example-EventMarker-DTN-Block: event marker stream from the stimulus presentation software (Unity in this case). It has two
  channels. This first is called DTN (with values 1, 2, and 3 for distractor, target, and novelty, respectively). This channel
  will be our labels for the trials, and in turn, labels for the our data in training the classifier. The second channel is
  BlockID. It has integer values going from 1 to 9, and each value corresponds to a block of trials. A position value indicates
  the start of a block and a negative value indicates the end of a block. We will use this information to split the blocks into
  training and testing blocks.

- Example-Video: the 400 by 400 screen capture from the stimulus presentation software. This is not used in the classifier but
  we will use it for visualization.

- Example-Video-Gaze-Pixel: two channels of gaze position in pixel coordinates. It tells use where participant's gaze lays in
  the screen capture. This is not used in the classifier but we will use it for visualization.


About the experiment
********************

Similar to the :ref:`simple ERP tutorial <Simple ERP tutorial>`, this experiment is a visual oddball task. Different objects are presented to the
the participant, who is instructed to count the number of times that a predefined target appears. Each appearance of
an object is called a *trial*. Multiple trials are grouped into a *block*. Each block contains 30 trials, and there
are a total of 9 blocks. Our goal is to build a classifier that can tell if a trial is a target or a distractor trial.
We will use the first 6 blocks for training, and the last 3 block for testing.

EEG classifier
**************

We will start by building a classifier that only uses EEG data. Use the following script,



Online vs offline inference
***************************







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