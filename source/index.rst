.. RealityNavigationDocs documentation master file, created by
   sphinx-quickstart on Wed Mar 24 13:36:31 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to RenaLabApp's documentation!
=================================================

About RenaLabApp
========================
RenaLabApp (Rena stands for reality navigation, our vision for this app is to help user navigate all sorts of real-time
data in the real world) is a Python-based App for visualizing, synchronizing, recording, and processing (i.e., make prediction)
data streams, camera, and screen capture in real-time.

RenaLabApp Users may easily integrate RenaLabApp into their data pipeline if
they are already using `Lab Streaming Layer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_ or ZMQ
to stream their data.

Download
-----------




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
   ViewInMatlab
   ContributeToThisDoc
   LICENSE
   PsychoPy


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
