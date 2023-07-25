Welcome to PhysioLabXR's documentation
=================================================

*physiological and neuroimaging experiments, made easy*
-------------------------------------------------------
+-----------------------------------------------+-----------------------------------------------+-----------------------------------------+--------------------------------------------------+
| .. image:: media/index_page/visualization.png | ..  image:: media/index_page/realtime_dsp.png | .. image:: media/index_page/replay.png  | .. image:: media/index_page/scripting.png        |
|    :width: 200                                |    :width: 200                                |    :width: 200                          |    :width: 200                                   |
|    :target: Visualization.html                |    :target: DSP.html                          |    :target: Replay.html                 |    :target: Scripting.html                       |
+-----------------------------------------------+-----------------------------------------------+-----------------------------------------+--------------------------------------------------+


PhysioLabXR is a pure Python application for running physiological experiment involving EEG, fNIRS, eyetracking, fMRI,
audiovisual recording from cameras and microphones, and more!
Tailored for academic researchers and industry practitioners, it supports multiple platforms (screen-based, VR, or AR)
and offers real-time visualization, synchronization, recording, and data processing (i.e., apply filters and run machine
learning models).

You may easily integrate RenaLabApp into your data pipeline if
they are already using `Lab Streaming Layer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_ or `ZMQ <https://zeromq.org/>`_
to stream your data. Otherwise you are welcome to create your own data stream via the `Data Stream API <DataStreamAPI.html>`_.

Download
-----------
Get the latest release of PhysioLabXR `here <https://github.com/ApocalyVec/PhysioLabXR/releases>`_. For the stable version, use `v0.1.0-beta <https://github.com/ApocalyVec/PhysioLabXR/tree/refs/tags/v0.1.0-beta>`_. The packaged release supports Windows, MacOS, and Linux.

Alternatively, you can run the application from the source. Refer to *Run from Source* in the `developer page <Developer.html>`_ for instructions.

Get Started with Examples
--------------------------
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


