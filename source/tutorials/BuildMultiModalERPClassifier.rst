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


About the experiment
********************

Similar to the simple ERP tutorial, this experiment is a visual oddball task. Different objects are presented to the
the participant, who is instructed to count the number of times that a predefined target appears. Each appearance of
an object is called a *trial*. Multiple trials are grouped into a *block*. Each block contains 30 trials, and there
are a total of 9 blocks. Our goal is to build a classifier that can tell if a trial is a target or a distractor trial.
We will use the first 6 blocks for training, and the last 3 block for testing.









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