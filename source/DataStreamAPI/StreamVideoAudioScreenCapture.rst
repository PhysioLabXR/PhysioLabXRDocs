
.. _stream video, audio, screen capture:

#####################################################
Using Video, Audio Input Devices, and Screen Capture
#####################################################


Video and audio input devices recognize by your OS are automatically detected and can be used as data sources.


.. _datastreamapi video devices:

Video Devices
----------------
PhysioLab\ :sup:`XR` automatically detects the video input devices
connected to your computer. Their name will be listed in the ``Add Stream`` dropdown as ``Camera <camera id>``.
To add an video input stream:

#. Click on the drop down of **Add Stream**  and select the video device you want to add.
#. Click on **Add** button.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/video_device.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>


Audio Input Devices
--------------------

Similar to the the video input devices, PhysioLab\ :sup:`XR` automatically detects the audio input devices
connected to your computer. Their name will be listed in the ``Add Stream dropdown``.
To add an audio input stream:

#. Click on the drop down of **Add Stream**  and select the audio device you want to add.
#. Once selected, a few new options settings will show up on the right of the dropdown.
#. Set the *sampling rate* (default: 8192), *frame/buffer* (default: 128), and *data type* (default: paInt16) of the audio device.
#. Click on **Add** button.

.. note::
    Please refer to the `PyAudio documentation <https://people.csail.mit.edu/hubert/pyaudio/docs/>`_ for more information about parameters for audio interface.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-audio" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/datastreamapi-audio-interface.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>



Screen Capture
----------------
Similar to the the video input devices, PhysioLab\ :sup:`XR` supports streaming screen capture. The name of the screen capture stream will be ``monitor 0`` as default.
To add an video input stream:

#. Click on the drop down of **Add Stream**  and select ``monitor 0``.
#. Click on **Add** button.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/screencap.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Refresh the list of devices
------------------------------

If you have connected a new device, you can refresh the list of devices by going to ``File > Settings > Video Device``,
and click ``Reload Video Devices``. Similarly, you can refresh the list of audio devices by going to
``File > Settings > Audio Device``, and click ``Reload Audio Devices``.

If your device is recognized by the OS, but not by PhysioLab\ :sup:`XR` and the data is not streamed correctly. Please
submit an issue `here <https://github.com/PhysioLabXR/PhysioLabXR/issues>`_.


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
            checkAndPlayVideo("autoplay-video-audio");
            checkAndPlayVideo("autoplay-video-lsl");
            checkAndPlayVideo("autoplay-video-zmq");
            // Add more videos as needed, using their respective video IDs
        });
    </script>



.. |ico6| image:: /media/start.svg
   :width: 20px
   :height: 20px


.. |stream_available| image:: /media/streamwidget_stream_available.svg
   :width: 20px
   :height: 20px

.. |stream_active| image:: /media/streamwidget_stream_viz_active.svg
   :width: 20px
   :height: 20px