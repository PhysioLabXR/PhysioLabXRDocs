***************
Recording Data
***************
RenaLabApp's recording feature lets you record multi-stream data with synchronized timestamps from various
sources, BioSemi and screen capturing for example. The recorded data can be stored as various data formats which you can choose from the
settings, including '.dats', '.m', '.p', '.csv', '.xdf'.

Get Started with a Simple Example
*********************************
In this example, we will record data from a randomly generated dummy stream as well as screen capturing.

Create a Dummy Stream
#####################
First, we will create a dummy stream to record.
Create a new python file, copy the following.

.. literalinclude:: LSLExampleOutlet.py
    :language: python
    :linenos:
    :lines: 1-100

Now run the script in your terminal with a command like ``python LSLExampleOutlet.py``

Record data
############

.. |ico0| image:: /media/stream_tab.png
   :height: 3ex

.. |ico1| image:: /media/start_button.png
   :height: 3ex

.. |ico2| image:: /media/Recording_Tab.png
   :height: 3ex

.. |ico3| image:: /media/recording_option.png
   :height: 3.5ex

.. |ico4| image:: /media/start_recording.png
   :height: 3ex

.. |ico5| image:: /media/stop_recording.png
   :height: 3ex

.. |ico6| image:: /media/start.svg
   :width: 20px
   :height: 20px

1. In the main window, navigate to the **stream tab** |ico0| located in the main window. In the **Add Stream** widget, type in 'Dummy-8Chan', which is the name of the stream we created in the previous step. Press the **Add** button or press **Enter** to add the stream. Now create a new stream named 'monitor 0'. This is the app's native stream name of screen capturing stream. The app now only supports this name as a screen capturing stream name, and only the main screen is captured if multiple screen is in use.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/Recording1.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

2. From the pop-up window, you should see a stream named 'Dummy-8Chan' and a stream named 'monitor 0' with some information about the streams. Press the **Start** button  |ico6| under 'Dummy-8Chan' stream to start receive the stream (no start button press needed for the screen capturing stream).

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/Recording2.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>
3. Now navigate to the **recording tab** |ico2| located on the upper left corner in the main window. (Optional) Click the **recording options** button |ico3| , you can modify the file Path you want to store your data and the data format you want to store.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/Recording3.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>
4. Click **Start recording** button |ico4| , the app will start recording the streams, and you can see the recorded time and file size in real time below.
5. Click **Stop recording** button |ico5| , the app will stop recording and store the recorded data to the path you specified.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/Recording45.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

.. note::
    Loading .dats and .csv files can be slow

6. Now your can navigate to **file**/**Show Recordings** to see the recorded files.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/Recording6.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Working with recorded data
################################
Recorded data can be loaded back for further analysis.

To use ReNa's native load functions, you can install our PhysioLabXR package

install: ``pip install PhysioLabXR``

And simply import our native load function, here is an example
.. code-block:: python

    from PhysioLabXR.rena.user_utils import stream_in

    data = stream_in('path/to/file')


Advanced
########

.dats serialization and Eviction Interval
******************************************
The recording is enabled by a serialization interface (RNStream) optimized for data with timestamps from multiple
sources (i.e., EEG, eyetracking and video recorded simultaneously)

Recording files is created by RealityNavigation uses .dats file format. .dats (dictionary of array and timestamps) is a binary file format used by RealityNavitaion to log the recorded data.
The structure of .dats is shown in the figure below

.. image:: media/RN_Stream.png
    :width: 1080

The file content is first segmented by eviction intervals. Within each interval, each type-length-
value (TLV) packet contains the data for individual streams. The figure shows the anatomy of a TLV packet. Starting with a delimiter sequence
called magic, the packet contains the data array and timestamps preceded by meta information: stream name, data type, number of dimensions, and
the shape of the data. When loading .dats back, the loader uses the dimension information to determine the number of bytes to read as data and
timestamp

Once recording starts, all the data streams are routed to a specialized buffer.
The buffer’s content is retained in the host’s memory until the eviction interval is hit. Default at one second, the
eviction interval controls how often we offload the buffer to the disk. When the data throughput are high, it is important
to evict the buffer in time to prevent out-of-memory. User may adjust the eviction interval in the settings to optimize
for their use case. During an eviction, each stream’s data and timestamps is appended to the file as a TLV packet

Developer
#########

`Go To Developer Page <Developer.html>`_