**********
Replaying
**********

PhysioLab\ :sup:`XR`'s replay feature enables users to play back data streams from past experiments, and if needed, test their data
processing script and algorithm in real-time as if the experiment is currently running

Learn From a Simple Example How to Record Data
*******************************************************
In this example, we will replay a real experiment with video stream, EEG data and eye-tracking data stream. If you want to learn more about the
experiment and how to do real-time processing, please refer to `FixationDetection <FixationDetection.html>`_ .

1. First, we need a experiment file recorded in a supported format. In this example, we will use a experiment file recorded in .p format.
Download the file `here (~3.7 GB) <https://drive.google.com/file/d/1-1YCCO4by9xpYRrz17glX9llAeg__ftZ/view?usp=drive_link>`_.

2. Now open PhysioLab\ :sup:`XR` and navigate to the ``Replay`` tab. Click on the ``Browse`` button and select the downloaded file.
After a few seconds, the file will be loaded and you should see a table showing informations of this recording looks like the following screen:

.. image:: media/replaytab.png
    :width: 1080

Let's take a closer look at what each column mean in this table:

- *Stream name*: The name of the stream. This is determined by the name you gave to the streams while recording.

- *Channels, Time points*: The number of channels and time points in the stream as a tuple.

- *Averaged sampling rate*: The averaged sampling rate of the stream calculated by :math:`\frac{length\ of\ the\ stream(in\ samples)}{duration\ of\ the\ stream(in\ seconds)}`. Note that this
  is just for reference and could be different from the actual sampling rate of the stream, especially when the stream's sample rate is not fixed. Event marker stream is an example. But
  a stream with 0 sample rate should indicate there's a problem with the stream.

- *Data type*: The data type of the stream. This is determined by the data type you choose while recording.

- *Stream interface*: The interface API to receive the stream. Currently, PhysioLab\ :sup:`XR` supports 2 interfaces: ``LSL`` and ``ZMQ``. This is not determined by the recording, you can
  whatever interface you want to receive the stream. However, we recommend using ``ZMQ`` interface for streams with a lot of channels, like video streams.

- *Include in replay?*: This is the checkbox to indicate whether this stream will be included in the replay. You can uncheck this box to exclude the stream from the replay.

3. Now, we can kick off the replay by clicking on the ``Start`` button. This will start the sender of the stream interface and take you back to the stream tab with a Playback window showing the
progress bar of the replay. You can pause the replay by clicking on the ``Pause`` button and resume it by clicking on the ``Resume`` button. You can also drag the progress bar to
any position to jump to that position in the replay. In the stream tab, PhysioLab\ :sup:`XR` will add every stream that is not already in your stream tab as you specified in the replay tab automatically.
However, if a stream is already in your stream tab, PhysioLab\ :sup:`XR` will not add it again nor update it with the settings you specified in replay tab. So keep in mind that yous should change the interface
type manually, otherwise the stream will not be received.

4. Click on the ``Start all`` button on the bottom left corner to start receive the stream and visualize it. You should see the EEG data and eye-tracking data stream. If the video stream is not
showing correctly, you should first setup the video stream. Click on the :math:`\dots` button on the top right of *Example-Video*'s widget to bring up the *Options Window*. It should have
already selected *Image* as the `plotting format <Stream.rst#plotting-formats>`_ because it has too many channels.
Enter 400 for both *Height* and *Width* to match the video's resolution. Select *bgr* in the image dropdown. Finally,
select *channel_first* in the *Channel format* dropdown. Return to *Stream Tab*, move your cursor to the lower left of the
*Example-Video*'s plot, click the *[A]* button that shows up to have the plot auto-scale to fit the window.
Now you should see the video from the participant's camera.
