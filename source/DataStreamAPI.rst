###############
Data Stream API
###############


Streams are the basic building blocks of any PhysioLab\ :sup:`XR` paradigm. Streams are connected to data sources that produces data
over time. PhysioLab\ :sup:`XR` has the following hooks to connect to data sources and create streams:

#. **Network**: connects to data streams broadcasted through the local area network.
   PhysioLab\ :sup:`XR` supports two popular network APIs for streaming experiment data: `LSL <DataStreamAPI.html#using-lsl>`_
   and `ZMQ <DataStreamAPI.html#using-zmq>`_
#. **Python API**: for devices that has Python API, such as `brainflow <https://brainflow.org/>`_ for
   `OpenBCI devices <openbci.com>`_, users can write their own data source script and connect it to PhysioLab\ :sup:`XR`
    through the scripting interface. `More information here <DataStreamAPI.html#write-your-data-source-script>`_.
#. **Video and audio devices**: for devices recognized by the OS as webcams or microphones, they will automatically
   appear on the list of devices available to stream in PhysioLab\ :sup:`XR`.


Using LSL
***********************

`LabStreamingLayer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_
is a network protocol for streaming time series data, mostly used for physiological experiments.
LSL has the following characteristics:

- Native support for many popular devices (`more information here on how to connect supported device with LSL <https://labstreaminglayer.readthedocs.io/info/supported_devices.html>`_).
- LSL is a cross-platform protocol, and has bindings for many programming languages.
- LSL outlets can carry meta information such as sampling rate and channel names.



Write your LSL Interface
--------------------------

Using ZMQ
***********************
For zmq, supports the subscriber-publisher socket pattern, because this pattern is more scalable when the
  cardinality of data is high and less error-prone in case of either the publisher or subscriber process crashes.
RenaLabApp robustly isolates the threads where the network interfaces reside, and notifies the user when network errors occur.


Write your ZMQ Interface
--------------------------

Choosing ZMQ ports
--------------------------

Write your data source script
********************************


Using Video and Audio Devices
********************************
