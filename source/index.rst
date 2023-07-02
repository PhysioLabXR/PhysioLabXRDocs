Welcome to RenaLabApp's documentation!
=================================================

About RenaLabApp
========================
RenaLabApp is a Python-based app for visualizing, synchronizing, recording, and processing physiological, neural, image, speech and behavior
data in real-time. The goal of Rena (stands for Reality Navigation) is to help you, whether a academia researcher, industry practitioner or hobbyist,
navigate a variety of real-time data).

RenaLabApp has built-in support for `Lab Streaming Layer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_
or `ZMQ <https://zeromq.org/>`_, to receive and process their data. All devices supported by LSL are automatically good to go
with RenaLabApp; `here's a complete list <https://labstreaminglayer.readthedocs.io/info/supported_devices.html>`_. RenaLabApp
also have native plugins for a another `list of devices <SupportedDevices.html>`_.

RenaLabApp excels when you working with multiple data streams coming from different sources, as

Download
-----------


Add Streams to RenaLabApp
-------------------------

Add a screen capture
~~~~~~~~~~~~~~~~~~~~

Add a webcam
~~~~~~~~~~~~

Add a LSL stream
~~~~~~~~~~~~~~~~

Add a ZMQ stream
~~~~~~~~~~~~~~~~

Replay from a file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Build your first pipeline with RenaLabApp

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


