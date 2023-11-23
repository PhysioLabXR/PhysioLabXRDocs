.. _customized data source:


#############
Use Lab Streaming Layer (LSL) & and ZeroMQ (ZMQ) to Implement Your Customized Data Source
#############

    Last Modified: 11/18/2023

.. contents:: Overview

*************
Introduction
*************

This document
will show you how to install and use `LSL <https://labstreaminglayer.readthedocs.io/info/getting_started.html>`_ and `ZMQ <https://zeromq.org/>`_
in Python and Unity (C#) to implement your customized data source.
For other languages, please refer to the `LSL <https://labstreaminglayer.readthedocs.io/info/getting_started.html>`_ and `ZMQ <https://zeromq.org/>`_
documentations.




Both LSL and ZMQ are open-source, cross-platform streaming tools.
They are widely used in the research community to stream data from various sources, such as EEG, eye-tracking, motion capture, etc.
Comparing with ZMQ, LSL is easier to implement and it has a built-in time synchronization mechanism. However,
LSL introduces additional overhead for time synchronization and metadata management.
If your application doesn't require these features, the simplicity of ZeroMQ may result in faster performance.
For example, our :ref:`Fixation Detection Tutorial <tutorial fixation detection>` use ZMQ to stream the camera data from Unity (C#) to PhysioLab\ :sup:`XR` (Python).





*************
Lab Streaming Layer (LSL)
*************



=============
LSL Outlet in Python
=============



=============
LSL Inlet in Python
=============



=============
LSL Inlet in Unity
=============



=============
LSL Outlet in Unity
=============


*************
ZeroMQ (ZMQ)
*************

=============
ZMQ Publisher in Python
=============

=============
ZMQ Subscriber in Python
=============

=============
ZMQ Publisher in Unity
=============

