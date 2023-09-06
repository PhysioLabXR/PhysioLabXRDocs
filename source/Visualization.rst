.. _feature visualization:

***************
Visualization
***************

Visualization is an essential aspect of data handling software,
and PhysioLab\ :sup:`XR` follows this convention. The primary entry point of PhysioLab\ :sup:`XR` is its ``stream tab``,
where users can connect to data sources by specifying the required parameters for each stream type (e.g., stream name for LSL streams,
as seen in section `LabStreamingLayer (LSL) <DataStreamAPI.html#use-lsl>`_). Once added, the **StreamWidget** is displayed in the ``stream tab``,
where users to customize their visualization.

.. note::
    The **StreamWidget** will check if the number of channels in the settings matches the number of channels received from the network, and the
    system will ask for confirmation if the user want to reset the settings to match the number of channels received from the network. This operation will reset all the
    settings to default, and the user will need to reconfigure the settings.

Grouping and Separating Channels
--------------------------------







































On launching, the landing page of the RenaLabApp is:

.. image:: media/RN_landing_page.png
    :width: 500

, where you can add webcam, screen capture, and `LSL data stream <https://labstreaminglayer.readthedocs.io/info/intro.html>`_:

.. image:: media/RN_add_stream.png
    :width: 500

Below is an example of RNApp monitoring eye tracking, head movement, and event marker for an experiment.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="_static/RN_multistream.mp4" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe>
    </div>

You can start testing your build with a dummy stream. Run this command from the repo's root to start an dummy stream::

   python exmaples/LSLExampleOutlet.py

Copy the stream name (yours might be different) paste it in 'Define a Stream to Add' in RNApp.

.. image:: media/RN_dummy_stream.png
    :width: 500


Then you should see the visualization of this dummy stream, which is sampled at 100Hz, with random numbers:

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="_static/RN_dummy_stream.mp4" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe>
    </div>

This covers the basic interaction of visualizing data streams and add camera captures. Read on to learn how to record synchronized
data streams, adding your own devices, and other information.


- Cross platform support
   The App supports Windows 10, MacOS, and most Linux distros (tested on Ubuntu 20.04 and Fedora 33).
   You will need to install the headless version of cv2 if you encountered xcb platform plugin not found problem, install with::

      pip install opencv-python-headless


Plot Options
*************