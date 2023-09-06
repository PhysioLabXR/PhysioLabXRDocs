#############################################
Real-time Fixation Detection
#############################################

Eye gaze reveals a lot about a person's cognitive processes. But raw gaze data is messy and hard to interpret: it's hard to tell
if someone is staring at something or just gliding by.
`Fixation <https://en.wikipedia.org/wiki/Fixation_(visual)>`_ detection is a common preprocessing step to filter gaze data and obtain the points the user had really paid attention to.
Read more about eyetracking and fixation detection `here <https://link.springer.com/article/10.3758/s13428-021-01762-8>`_.

In this tutorial, we will learn how to create a pipeline in PhysioLab\ :sup:`XR` to detect
fixations in real-time using one of the most popular algorithms: `dispersion-thresholding
fixation identification <https://dl.acm.org/doi/abs/10.1145/355017.355028>`_, or I-DT for short.

.. raw:: html

    <figure>
        <div style="position: relative;">
            <video id="autoplay-video1" autoplay controls loop muted playsinline style="width: 100%;">
                <source src="_static/fd-example-teaser.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>
        <figcaption style="margin-top: 10px;">
            Real-time fixation detection, the middle stream is the gaze vector captured by the eyetracking in the VR headset. The left stream gives 1 when there is a fixation and 0 otherwise. The video on the right shows the participant's camera. The circle in the video shows where the participant is looking at. The circle turns red when there is a fixation. You will see most of the fixation happens when the participant is looking at some objects in the scene.
        </figcaption>
    </figure>


We will start by looking at a recording of a Virtual Reality experiment, where a participant is searching for target
items in a visual search task. We will then develop a script to detect where the participant fixated
in real-time and visualize the results.


.. note::
    This tutorial assumes you have already installed PhysioLab\ :sup:`XR` and have basic knowledge of Python.
    If you are new to PhysioLab\ :sup:`XR`, we recommend you to start with the `simple ERP example <index.html#get-started-with-a-simple-example>`_.


Download the data
************************
Download the recording from `here (~3.7 GB) <https://drive.google.com/file/d/1-1YCCO4by9xpYRrz17glX9llAeg__ftZ/view?usp=drive_link>`_.
It is a rather big file because it contains the video frames from the participant's camera.

Replaying the recording
************************

We will start by replaying the recording to see what it looks like.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video2" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/fd-example-replay.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Follow these steps to replay the experiment:

#.
    Go to the *Replay Tab* and click the *Browse* button. Select the downloaded recording file and click *Open*. It may
    take a few seconds to load the recording.

    Once loaded, you will see the list of data streams that the recording contains. There are *Example-BioSemi-Midlines*,
    *Example-Eyetracking*, *Example-Video-Gaze-Pixel*, and *Example-Video*.

#.
    In the second column of the *Stream Info Pane*,
    you can see the number of channels and the time points each stream has in this recording. Among them, *Example-Video* has
    480,000 channels, corresponding to 400 height and width with 3 color channels (*400×400×3*). This will be too much for
    `LabStreamingLayer (LSL) <DataStreamAPI.html#use-lsl>`_ to handle. We will change its *Stream Interface* to ZMQ. Leave the
    `ZMQ port number <technical_notes/ZMQInterface.html#port-numbers>`_ as default.

#. We don't need EEG for this tutorial, check off *Example-BioSemi-Midlines*, so it is not included in the replay.

#.
    Click on the *Start Replay* button. PhysioLab\ :sup:`XR` will switch to the *Stream Tab* and open the *Playback Window*.
    Click the *Start All* button on the lower left corner to start plotting the streams. You should see *Example-Video-Gaze-Pixel*,
    and *Example-Eyetracking* started visualizing.

#.
    To see the video from the stream *Example-Video*, we need to let the App know how we want to visualize it. Click on the
    :math:`\dots` button on the top right of *Example-Video*'s widget to bring up the *Options Window*. It should have
    already selected *Image* as the `plotting format <Stream.rst#plotting-formats>`_ because it has too many channels.
    Enter 400 for both *Height* and *Width* to match the video's resolution. Select *bgr* in the image dropdown. Finally,
    select *channel_first* in the *Channel format* dropdown. Return to *Stream Tab*, move your cursor to the lower left of the
    *Example-Video*'s plot, click the *[A]* button that shows up to have the plot auto-scale to fit the window.
    Now you should see the video from the participant's camera.

A bit more on the three streams being replayed here, they are:

*
    *Example-Video* has 480,000 channels. It is the video frames from the participant's camera. The video is 400 by 400 pixels
    with 3 color channels.

*
    *Example-Video-Gaze-Pixel* has two channels with values between 0 and 399. They are synchronized with *Example-Video*
    and the values are the pixel coordinates of where
    the participant is looking in the video frames.
*
    *Example-Eyetracking* has four channels. The first three channels are the gaze vectors' x, y and z. The last channel
    shows if this frame of data is valid. It has values of either 2 or 0. A value of 2 means the eyetracking data is valid
    at this frame, and 0 means data loss, possibly a result of participant blinking.



.. note::
    The recording is about 6 minutes long. If anytime the replay is finished while you are working on this tutorial, you can
    restart the replay by clicking the *Start Replay* button in the *Replay* tab. Or the *Start* button in the *Playback Window*.
    Learn more about the *Replay* feature `here <Replay.html>`_.

Gap filling the gaze sequence
******************************

We will now add a *script* to process the eyetracking data. The first step towards fixation detection is filling the
glitches that result in gaps in the gaze sequence. In short, the :ref:`gap-filling algorithm <scripting api gap fill>` looks at each interval where gaze
data are invalid; if the gap is smaller than a threshold (default at 75 ms), it will consider this gap a system glitch and fill it by interpolating. If the
gap is larger than 75 ms, it will consider this gap a blink and leave it as a gap with NaN values.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video2" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/fd-example-gap-fill.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

#.
    Go to the `Script Tab <Scripting.html>`_ and click the *Add* button. A *Script Widget* will be added to the *Script Tab*.
    Click the *Create* button in the *Script Widget*. In the file dialog, select the folder where you want to save the script.
    Name it *FixationDetection* and click *Save* in the file dialog. The script should be opened in your default editor.
    Replace the template code with the following script:

    .. code-block:: python

        from rena.scripting.RenaScript import RenaScript
        from rena.scripting.physio.eyetracking import gap_fill


        class FixationDetection(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)

            # Start will be called once when the run button is hit.
            def init(self):
                self.max_gap_time = 0.075  # the maximum time for gap to be considered a glitch that will be filled, gap longer than this will be ignored for they are likely to be blinks

                self.gaze_channels = ['x', 'y', 'z', 'status']  # the channels of the gaze stream, x, y, z are the 3D gaze vector, status is the validity of the gaze sample
                self.gaze_status = {'valid': 2, 'invalid': 0}  # the status of the gaze sample, 2 is valid, 0 is invalid

                self.gaze_stream_name = 'Example-Eyetracking'  # the name of the gaze stream

            # loop is called <Run Frequency> times per second
            def loop(self):
                # gap filling
                # if the last sample is valid, we go back and see if there's any gap needs to be filled
                # once the gaps are filled we send the gap-filled data and clear 'em from the buffer
                if self.gaze_stream_name in self.inputs.keys():  # first check if the gaze stream is available
                    gaze_timestamps = self.inputs[self.gaze_stream_name][1]  # we the gaze stream using key self.gaze_stream_name, the first element of the value is the data, the second element is the timestamps
                    gaze_status = self.inputs[self.gaze_stream_name][0][self.gaze_channels.index('status')]  # we get the status channel of the gaze stream
                    gaze_xyz = self.inputs[self.gaze_stream_name][0][:3]  # get the xyz channels of the gaze stream

                    if gaze_status[-1] == self.gaze_status['valid']:  # and starting_invalid_duration > self.max_gap_time:  # if the sequence starts out invalid, we must wait until the end of the invalid
                        gap_filled_xyz = gap_fill(gaze_xyz, gaze_status, self.gaze_status['valid'], gaze_timestamps, max_gap_time=self.max_gap_time)  # fill the gaps!
                        self.outputs['gap_filled_xyz'] = gap_filled_xyz  # send the gap-filled data so we can see it in the plotter
                        self.inputs.clear_stream_buffer(self.gaze_stream_name)  # clear the buffer of the gaze stream as the gaps are filled, we don't need to process them again

            # cleanup is called when the stop button is hit
            def cleanup(self):
                print('Cleanup function is called')


#.
    Save the script and return to the *Scripting Tab*. Below the text box with the path to your script, change the
    *Run Frequency* to 30. This will make the script run at a maximum frequency of 30 times per second. It doesn't have
    to run at this frequency, but it helps with producing smoother data when plotted in the *Stream Tab*.

#.
    We need to add the eyetracking stream as an input to the script. Type *Example-Eyetracking* in the *inputs text box*.
    Click the *Add* button in the *Inputs* pane to add the stream as an input to the script.

#.
    To visualize the gap-filled data, we need to add it as an output of the script. Type *gap_filled_xyz* in the outputs
    text box and click the *Add* button in the *Outputs* pane. After adding, change the number of channels for this output
    to 3 in its widget. The three channels are the x, y, and z coordinates of the gap-filled gaze vector.

#.
    Now we can run the script by clicking the *Run* button in the *Scripting Widget*. You should see the console producing
    messages like this:

    .. code-block:: none

        With max gap duration 0.175ms, 1 gaps are interpolated among 1 gaps,
        with interpolated gap with mean:median duration <x>ms:<x>ms,
        and ignored gap with mean:median duration <x>ms:<x>ms

#.
    To visualize the gap-filled data, go to the *Stream Tab*. Type *gap_filled_xyz* in the add text box. Click *Add*. It will be
    added as a new stream. Start the stream. Once data is available, the app will ask if you want to reset the number of channels to match what's found on the network.
    Click *Yes*, because the default number of channels for newly added streams is 1. Now we set it to 3 to match the output from
    the script. Once the number of channels is reset, data will start plotting. Go to its options and change its nominal sampling rate to
    200 to match the eyetracking stream.

In the gap-filled data, now processed in real-time and plotted in sync with the original data, you should see when the original
data is invalid (the last channel is 0). The gap-filled data will either have interpolated values if the gap is small enough, or
no data if the gap is too large.

.. note::
    The script runs on a replayed experiment in this tutorial. But you can have it run
    in real-time for your experiments.


Detect fixations using I-DT
******************************

Next up, we will build on top of the previous script to detect fixations using the I-DT algorithm.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video3" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/fd-example-fd.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

#.
    If your script is still running, you can click the *Stop* button to stop it in its *Scripting Widget*.
    Replace the following code in the script and save it.

    .. code-block:: python

        from rena.scripting.RenaScript import RenaScript
        from rena.scripting.physio.eyetracking import gap_fill, fixation_detection_idt
        from rena.utils.buffers import DataBuffer


        class FixationDetection(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)

            # Start will be called once when the run button is hit.
            def init(self):
                self.max_gap_time = 0.075  # the maximum time for gap to be considered a glitch that will be filled

                self.gaze_channels = ['x', 'y', 'z', 'status']  # the channels of the gaze stream, x, y, z are the 3D gaze vector, status is the validity of the gaze sample
                self.gaze_status = {'valid': 2, 'invalid': 0}  # the status of the gaze sample, 2 is valid, 0 is invalid

                self.gaze_stream_name = 'Example-Eyetracking'  # the name of the gaze stream

                self.fixation_timestamp_head = 0  # the timestamp of the beginning of the last fixation window

                self.processed_gaze_buffer = DataBuffer(stream_buffer_sizes={'fixations': 1000, 'gap_filled_xyz': 1000})  # buffer to store the preprocessed gaze data, including the gap-filled gaze vectors and the fixation sequences


            # loop is called <Run Frequency> times per second
            def loop(self):
                # gap filling
                # if the last sample is valid, we go back and see if there's any gap needs to be filled
                # once the gaps are filled we send the gap-filled data and clear 'em from the buffer
                if self.gaze_stream_name in self.inputs.keys():  # first check if the gaze stream is available
                    gaze_status = self.inputs[self.gaze_stream_name][0][self.gaze_channels.index('status')]  # we the gaze stream using key self.gaze_stream_name, the first element of the value is the data, the second element is the timestamps
                    gaze_timestamps = self.inputs[self.gaze_stream_name][1]  # we get the status channel of the gaze stream
                    gaze_xyz = self.inputs[self.gaze_stream_name][0][:3]  # get the xyz channels of the gaze stream

                    if gaze_status[-1] == self.gaze_status['valid']:  # if the sequence starts out invalid, we must wait until the end of the invalid
                        gap_filled_xyz = gap_fill(gaze_xyz, gaze_status, self.gaze_status['valid'], gaze_timestamps, max_gap_time=self.max_gap_time, verbose=False)  # fill the gaps!
                        self.processed_gaze_buffer.update_buffer({'stream_name': 'gap_filled_xyz', 'frames': gap_filled_xyz, 'timestamps': gaze_timestamps})  # add the gap filled data to the buffer, so we can use it for fixation detection
                        self.outputs['gap_filled_xyz'] = gap_filled_xyz  # send the gap-filled data so we can see it in the plotter
                        self.inputs.clear_stream_buffer(self.gaze_stream_name)  # clear the gaze stream, so we don't process the same data again, the fixation detection will act on the gap filled data

                    # up to the point of the last gap filled index, we detect fixation. The idt window ends at the gap filled index
                    fixations, last_window_start = fixation_detection_idt(*self.processed_gaze_buffer['gap_filled_xyz'], window_size=self.params['idt_window_size'], dispersion_threshold_degree=self.params['dispersion_threshold_degree'], return_last_window_start=True)
                    self.outputs['fixations'] = fixations[0:1]  # send the fixations, we grab the first column of the result, the second column are the timestamps
                    self.processed_gaze_buffer.clear_stream_up_to_index('gap_filled_xyz', last_window_start)  # now clear the gap filled data up to the last window start, so we don't process the same data again

            # cleanup is called when the stop button is hit
            def cleanup(self):
                print('Cleanup function is called')

#.
    The I-DT algorithm has two parameters often tuned for different experiments to get the best results. They are

        * idt window size: the window size in which the dispersion is calculated. The default value is 175 ms.
        * dispersion threshold degree: the threshold of the dispersion in the window to be considered a fixation. The default value is 0.5 degrees.

    We can expose these parameters through the *Parameters* tab in the *Scripting Widget* so we can tune them in real-time.
    Go to the *Parameters* tab and add the following parameters:

        * idt_window_size: set its type to float and enter the starting value of 0.175 seconds.
        * dispersion_threshold_degree: also set its type to float, and we will start with a value of 0.5 degrees.

#.
    Similar to adding the gap-filled data to the output, we now add the fixation result to the output so we can see it in the *Stream Tab*.
    Type *fixations* in the *Output* box and click the *Add* button. We will leave its number of channels to be 1: a value of 1
    means fixation, and a value of 0 means non-fixation.

#.
    Start the script and go to the *Stream Tab*. Add *fixations* as a new stream just as you did for gap_filled_xyz.
    Start plotting and go to its options window for this stream and change the nominal sampling rate to 200.

Now you should see the fixation as they are detected in real-time. In *Settings* under *Streams*,
we can change the *Line chart visualization mode* from *in-place* to *continuous* to get a clearer view of the fixations
along with the gaze data.


See it in action, add a fixation indicator to the video
********************************************************
As the last step, and for our visualization purposes, we will add a fixation indicator to the video and see how it looks like.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video4" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/fd-example-video.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

#. Same as before, we modified the script to take in the video frames. Copy and replace the following script:

    .. code-block:: python

        import cv2
        import numpy as np

        from rena.scripting.RenaScript import RenaScript
        from rena.scripting.physio.eyetracking import gap_fill, fixation_detection_idt
        from rena.scripting.physio.utils import time_to_index
        from rena.utils.buffers import DataBuffer


        class FixationDetection(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)

            # Start will be called once when the run button is hit.
            def init(self):
                self.max_gap_time = 0.075  # the maximum time for gap to be considered a glitch that will be filled

                self.gaze_channels = ['x', 'y', 'z', 'status']  # the channels of the gaze stream, x, y, z are the 3D gaze vector, status is the validity of the gaze sample
                self.gaze_status = {'valid': 2, 'invalid': 0}  # the status of the gaze sample, 2 is valid, 0 is invalid

                self.gaze_stream_name = 'Example-Eyetracking'  # the name of the gaze stream

                self.fixation_timestamp_head = 0  # the timestamp of the beginning of the last fixation window

                self.processed_gaze_buffer = DataBuffer(stream_buffer_sizes={'fixations': 1000, 'gap_filled_xyz': 1000})  # buffer to store the preprocessed gaze data, including the gap-filled gaze vectors and the fixation sequences
                self.frame_gaze_pixel_stream_name = 'Example-Video-Gaze-Pixel'  # the name of the frame gaze pixel stream, the stream tells us where the gaze is on the 400x400 video frame

                self.video_stream_name = 'Example-Video'  # the name of the video stream
                self.video_shape = (400, 400, 3)  # the shape of the video stream, because all inputs are flattened as they comes in, we need to reshape the video frames to be able to put shapes on them
                self.fixation_circle_color = (255, 0, 0)  # when the video frame's timestamp matches a fixation's, we put a red circle at the pixel location of where the gaze is
                self.gaze_circle_color = (0, 0, 255)  # the there's no fixation, we put a blue circle at the gaze location


            # loop is called <Run Frequency> times per second
            def loop(self):
                # gap filling
                # if the last sample is valid, we go back and see if there's any gap needs to be filled
                # once the gaps are filled we send the gap-filled data and clear 'em from the buffer
                if self.gaze_stream_name in self.inputs.keys():  # first check if the gaze stream is available
                    gaze_status = self.inputs[self.gaze_stream_name][0][self.gaze_channels.index('status')]  # we the gaze stream using key self.gaze_stream_name, the first element of the value is the data, the second element is the timestamps
                    gaze_timestamps = self.inputs[self.gaze_stream_name][1]  # we get the status channel of the gaze stream
                    gaze_xyz = self.inputs[self.gaze_stream_name][0][:3]  # get the xyz channels of the gaze stream

                    if gaze_status[-1] == self.gaze_status['valid']:  # if the sequence starts out invalid, we must wait until the end of the invalid
                        gap_filled_xyz = gap_fill(gaze_xyz, gaze_status, self.gaze_status['valid'], gaze_timestamps, max_gap_time=self.max_gap_time, verbose=False)  # fill the gaps!
                        self.processed_gaze_buffer.update_buffer({'stream_name': 'gap_filled_xyz', 'frames': gap_filled_xyz, 'timestamps': gaze_timestamps})  # add the gap filled data to the buffer, so we can use it for fixation detection
                        self.outputs['gap_filled_xyz'] = gap_filled_xyz  # send the gap-filled data so we can see it in the plotter
                        self.inputs.clear_stream_buffer(self.gaze_stream_name)  # clear the gaze stream, so we don't process the same data again, the fixation detection will act on the gap filled data

                    # up to the point of the last gap filled index, we detect fixation. The idt window ends at the gap filled index
                    fixations, last_window_start = fixation_detection_idt(*self.processed_gaze_buffer['gap_filled_xyz'], window_size=self.params['idt_window_size'], dispersion_threshold_degree=self.params['dispersion_threshold_degree'], return_last_window_start=True)
                    self.processed_gaze_buffer.update_buffer({'stream_name': 'fixations', 'frames': fixations[0:1], 'timestamps': fixations[1]})  # add the gap filled data to the buffer, so we can use it for fixation detection
                    self.outputs['fixations'] = fixations[0:1]  # send the fixations, we grab the first column of the result, the second column are the timestamps
                    self.fixation_timestamp_head = self.processed_gaze_buffer['gap_filled_xyz'][1][last_window_start]  # update the gaze timestamp head, so we can release video frames up to this timestamp
                    self.processed_gaze_buffer.clear_stream_up_to_index('gap_filled_xyz', last_window_start)  # now clear the gap filled data up to the last window start, so we don't process the same data again

                # release video frames up to the processed gaze timestamp, but we only release one video frame per loop
                # we loop through the video frames, if the timestamp of the video frame is less than the timestamp of the last fixation, we release the video frame and remove it from the buffer
                # we keep doing this until the timestamp of the video frame is greater than the timestamp of the last fixation or there's no more video frames
                while self.video_stream_name in self.inputs.keys() and len(self.inputs[self.video_stream_name][1]) > 0 and self.inputs[self.video_stream_name][1][0] < self.fixation_timestamp_head:
                    video_frames = self.inputs[self.video_stream_name][0]  # get the video frames
                    frame_pixels = self.inputs[self.frame_gaze_pixel_stream_name][0]  # find the frame pixel corresponding to the video timestamp
                    frame_pixel_timestamps = self.inputs[self.frame_gaze_pixel_stream_name][1]  # get the timestamps of the frame pixels

                    # we first look at the first frame in the buffer, we already know it's timestamp is less than the timestamp of the last fixation from the while condition
                    this_frame = video_frames[:, 0].reshape(self.video_shape).copy()  # take the first frame in the buffer, make a copy so the data is contiguous
                    this_frame_timestamp = self.inputs[self.video_stream_name][1][0]  # get the timestamp of the first frame in the buffer
                    this_frame_pixel = frame_pixels[:, frame_pixel_timestamps == this_frame_timestamp]  # get where the participant is look at in pixel coordinates

                    # find the closest fixation to the current video frame, we need to call time_to_index because the timestamps of the fixations are not the same as the timestamps of the video frames
                    # this is different from the frame pixel in the line above, whose timestamps are the same as the video frames and we can find exact matches
                    fixation_index = time_to_index(self.processed_gaze_buffer['fixations'][1], this_frame_timestamp)  # find the index of the closest fixation to the current video frame
                    is_fixation = self.processed_gaze_buffer['fixations'][0][:, fixation_index][0]  # find the fixation value
                    color = self.fixation_circle_color if is_fixation else self.gaze_circle_color  # if the participant is fixating, we draw a red circle, otherwise we draw a green circle
                    if this_frame_pixel.shape[1] > 0:  # if we can find a matching gaze coordinate, then we draw a circle on the video frame
                        cv2.circle(this_frame, np.array(this_frame_pixel[:, 0], dtype=np.uint8), 10, color, 2)  # draw a circle on the video frame
                    self.outputs['gaze_processed_video'] = this_frame.reshape(-1)  # send the video frame to the plotter
                    self.inputs.clear_stream_up_to_index(self.video_stream_name, 1)  # remove the first video frame from the buffer
                    self.processed_gaze_buffer.clear_stream_up_to_index('fixations', fixation_index)  # also remove the fixation up to the video frame we just released, we don't need it anymore
                    self.inputs.clear_stream_up_to(self.frame_gaze_pixel_stream_name, this_frame_timestamp)  # do the same for frame pixel, but we use the timestamp of the video frame

            # cleanup is called when the stop button is hit
            def cleanup(self):
                print('Cleanup function is called')

#.
    Before we start the modified script, we need to add two additional inputs, the *video* stream and the *gaze pixel on frame* stream.
    In the *Scripting Widget*, add *Example-Video* and *Example-Video-Gaze-Pixel* as inputs.

#.
    We will also add an output to stream out the processed video frames. Type *gaze_processed_video* in the *output* box and click *Add*.
    Change the number of channels to 480000, to match the original video. Change this output's stream interface to *ZMQ* and
    data type to *uint8*.

#.
    Start the script and go to the *Stream Tab*. Add the *gaze_processed_video* stream. Start this stream and similar to what we
    did for the original video, go to its options and set both its width and height to 400, image format to *bgr*, and channel
    format to *channel first*.

Now you should see the processed video in the *Stream Tab*, with the gaze indicator showing where the participant is looking at.
The gaze indicator will turn red when a fixation is detected, and blue when the participant is not fixating.

The video with fixation indicator will be a slightly delayed version of the original video, because
the fixation detection algorithm needs to process the gaze data first. To better look at the videos, we can pop out all
other streams except the original and processed videos, and look at them side by side.

So there you have it, a real-time fixation detection pipeline that can be adapted to your applications.


Further Information
*****************************
Now you have learned how to detect fixations in real-time using PhysioLab\ :sup:`XR`.
You can apply it to your experiment and use fixation to study user cognition, in tasks such as
`visual search <https://jov.arvojournals.org/article.aspx?articleid=2191835>`_ and
`reading <https://www.taylorfrancis.com/chapters/edit/10.4324/9781315630427-19/eye-movements-reading-tutorial-review-keith-rayner-alexander-pollatsek>`_,
and `scene perception <https://d1wqtxts1xzle7.cloudfront.net/1961773/Richardson_Spivey04b-libre.pdf?1390824253=&response-content-disposition=inline%3B+filename%3DEye_Tracking_Research_Areas_and_Applicat.pdf&Expires=1691505683&Signature=dHPTX9IHgmJj-4tc1UsRIFpcz7UiOEwfd~Rrkf5UF2vPVO-cuEjtdD549La20ThqVEClaqTeP-VlzAXkX908fASAkOZNvqFYtlJOUtH1auySgodYXD-ECNm0s2iJYKXbF0RiEDrfW0PJe-u5sdTewMtv3ExFjq8F12htEd5yAV2Fbxz-jpIJIdA8-U0b9ogY7hUYLFM8l1Hr7JfTRP6mZdoRzoiBmFIOJ1dryJRxNDDncGUI7Pc~KRAaAuyUp~l7UeUgDC64Vw62g1DY-AD3xhSuAnF4X8d2ThZCP7lbxG9K0gNjFqNScQ48YHaHOIIqmZ-mi6VpGvGOLSKw8PjBtw__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA>`_.


`Learn more about how scripting works <Scripting.html>`_.



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
            checkAndPlayVideSo("autoplay-video2");
            checkAndPlayVideo("autoplay-video3");
            checkAndPlayVideo("autoplay-video4");
            // Add more videos as needed, using their respective video IDs
        });
    </script>