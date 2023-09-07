.. _feature visualization:

***************
Visualization
***************



.. |ico0| image:: /media/stream_tab.png
   :height: 3ex

.. |ico1| image:: /media/popwindow_button.png
   :height: 3ex







Visualization is an essential aspect of data handling software,
and PhysioLab\ :sup:`XR` follows this convention. The primary entry point of PhysioLab\ :sup:`XR` is its ``stream tab``,
where users can connect to data sources by specifying the required parameters for each stream type (e.g., stream name for LSL streams,
as seen in section `LabStreamingLayer (LSL) <DataStreamAPI.html#use-lsl>`_). Once added, the **StreamWidget** is displayed in the ``stream tab``,
where users to customize their visualization.




Base Stream Widget
=============
The **BaseStreamWidget** is the primary visualization tool in PhysioLab\ :sup:`XR`. It is a Qt widget that can be added to any Qt application.
There are four sub-classes of the **BaseStreamWidget**:

1. **LSLWidget**: for visualizing LSL streams
2. **ZMQWidget**: for visualizing ZMQ streams
3. **AudioInputDeviceWidget**: for visualizing audio input device streams(e.g., microphone, etc.)
4. **VideoWidget**: for visualizing video streams(e.g., webcam, monitor, etc.)


  Please refer to the `DataStreamAPI <DataStreamAPI.html>`_ for more information about each stream type.

Each stream widget is responsible for visualizing the corresponding stream type. For example, the **LSLWidget** is responsible for visualizing LSL streams.
The **LSLWidget**, **ZMQWidget**, and **AudioInputDeviceWidget** are all based on the **BaseStreamWidget** have the same visualization features while the **VideoWidget** is only for video visualization.
Next, we will discuss the features that can be configured by user in each widget type.


**LSLWidget**, **ZMQWidget**, and **AudioInputDeviceWidget**
------------------------------------------------------------
Those three widgets share the same set of visualization features and settings.

Visualization Format:
~~~~~~~~~~~~~~~~~~~~~
There are four types of visualization formats in PhysioLab\ :sup:`XR`, and they are all supported by the **LSLWidget**, **ZMQWidget**, and **AudioInputDeviceWidget**:
User can select the visualization format for each individual plotting group by clicking on the tab with the corresponding plotting format or specify them in the `options window`.

.. note::
    The **LSLWidget** and **ZMQWidget** will check if the number of channels on visualization matches the number of channels received from the network when user click on the `add` button,
    and the system will ask for confirmation if the user want to reset the settings to match the number of channels received from the network. This operation will reset all the
    settings to default, and the user will need to reconfigure the settings.



Line Chart
~~~~~~~~~~
Line Chart is effective for disclosing temporal trends.
It is a commonly used format for time series such as most neuroelectrical signals,
eyetracking, and other physiological signals when the
researcher is interested in observing changes in amplitude over a period.

 Configurable Parameters:
    - *Nominal Sampling Rate (Int)*: The nominal sampling rate of the stream. This parameter is used to calculate the x-axis scale.
    - *Seconds of Data to Display(Int)*: The number of seconds of data to display on the plot. This parameter is used to calculate the x-axis scale.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video4" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/Visualization-LineChart.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>




Image
~~~~~~
Image with 2D or 3D with color channels can be plotted by data sources as images accordingly,
such as those obtained from medical imaging devices.

 Configurable Parameters:
    - *Width (Int)*: The width of the image in pixels.
    - *Height (Int)*: The height of the image in pixels.
    - *Image format (Enum)*: Pixel Map, BGR, or RGB.
    - *Display scaling* (Int): The scaling factor of the image.
    - *Display value min (Float)*: The minimum value of the image. (Pixel Map only, the value is used for min-max normalization)
    - *Display value max (Float)*: The maximum value of the image. (Pixel Map only, the value is used for min-max normalization)
    - *Color map (Enum)*: The color map of the image. (Pixel Map only, e.g., GRAY, VIRIDIS, etc.)) Please refer to `matplotlib color map <https://matplotlib.org/stable/tutorials/colors/colormaps.html>`_ for more information.

Width x Height x Color Channels = Number of channels in the group. For example, if and RGB image with with original shape of (50, 60, 3),
the number of channels in the group is 50 x 60 x 3 = 900,
and the Width is 50, the Height is 60, and the Image format should be RGB.

.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video5" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/Visualization-Image.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>

.. note::
    The stream with channel number larger than 1024 will be plotted as image only due to the performance issue, and other formats will be disabled.
    Plotting large number of channels as other formats will cause the application to freeze.
    We highly recommend using the ZMQ stream for image visualization because the LSL stream is
    not optimized for transmitting data with large channel number.



Bar Chart
~~~~~~~~~
Bar Chart plots one frame of data at a time,
making it suitable for scenarios where temporal changes are less relevant.
For example, researchers can employ this format to visualize the predictions generated by a machine learning model,
with each class's probability represented by a distinct bar.

  Configurable Parameters:
    - *Plot Range Min (Float)*: The minimum value of the y-axis.
    - *Plot Range Max (Float)*: The maximum value of the y-axis.

.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video6" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/Visualization-BarChart.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>


Spectrogram
~~~~~~~~~~~
Spectrogram or Short-Time Fourier Transform (STFT) prove valuable for visualizing the spectral content of a signal.
While the Fourier Transform (FT) plots the present power spectral density, the spectrogram shows its variation over time.
They are commonly employed in the analysis of EEG and audio data.
Researchers can leverage PhysioLab\ :sup:`XR` spectrogram visualization to gain insights into the frequency components and their changes across time.

  Configurable Parameters:
    - *Nominal Sampling Rate (Int)*: The nominal sampling rate of the stream. This parameter is used to calculate the x-axis scale.
    - *Seconds of Data to Display(Int)*: The number of seconds of data to display on the plot. This parameter is used to calculate the x-axis scale.
    - *Time per segment (Seconds in float)*: The window size of the STFT in seconds.
    - *Overlap between segment (Seconds in float)*: The overlap between each window in seconds.
    - *Color map (Enum)*: The color map of the spectrogram. (e.g., GRAY, VIRIDIS, etc.)) Please refer to `matplotlib color map <https://matplotlib.org/stable/tutorials/colors/colormaps.html>`_ for more information.
    - *Spectrogram Display Min Percentile (Int)*: The minimum percentile of the displayed frequency.
    - *Spectrogram Display Max Percentile (Int)*: The maximum percentile of the displayed frequency.


.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video7" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/Visualization-Spectrogram.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>


**VideoWidget**
------------------------------------------------------------
**VideoWidget** is used to visualize the video streams from screen capture devices and cameras.
Unlike the other visualization widgets, **VideoWidget** only supports the Image format, and the image format can be BGR or RGB.

    Configurable Parameters:
        - *Image Scale (Int)*: The percentage of the image resolution. (e.g., 100 means 100% of the original resolution, 50 means 50% of the original resolution)
        - *Channel Order (Enum)*: BGR or RGB.

.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video8" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/Visualization-VideoWidget.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>




Grouping and Separating Channels:
--------------------------------
Channels within the same stream can
be organized into groups thus plotted in separate plots.
This feature is particularly helpful when the channels measure different
phenomena with different vertical scales. For example,
given a stream of a six-degree-of-freedom inertia-measurement unit,
the user can group the gyro-axis and accelerometer-axis channels together.
Some device streams include the timestamps in their data frames that are measured
in epoch time that reaches up to 1e9 if represented by an float in seconds.
If plotted as line char, the different scales will flatline the more important
data channels when plotted into the same graph.
Additionally, the user can rename the channels and groups to make the plots more readable, while the original channel index decoded from the stream is preserved.
Furthermore, the user have the flexibility to hide specific groups or channels from the plots,
reducing clutter and optimizing the plotting area.


.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video6" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/Visualization-GroupingAndSeparatingChannels.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>




.. note::
    You cannot modify the channel name and group name in the image format group.
    To modify the channel name and group name, you need to switch to other plotting format first.

Organizing Multiple Streams
~~~~~~~~~~~~~~~~~~~~~~~~~~~
PhysioLab\ :sup:`XR` is designed to cater to experiments that involve multiple data sources,
aligning with the demands of modern neuroscience and HCI research.
While the software can also be utilized for single data source experiments,
many of its features truly shine when handling multiple streams concurrently,
such as EEG with eyetracking and video with audio.

To help researchers inspect multiple streams simultaneously, you can click on the ``pop window`` |ico1| button on the top right conor of the **StreamWidget** to pop out each stream and rearrange them on your screen.






.. raw:: html

        <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
            <video id="autoplay-video8" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
                <source src="_static/Visualization-OrganizingMultipleStreams.mp4" type="video/mp4">
                Your browser does not support the video tag.
            </video>
        </div>





































