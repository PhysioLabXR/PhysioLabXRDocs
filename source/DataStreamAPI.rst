###############
Data Stream API
###############


Streams are the basic building blocks of any PhysioLab\ :sup:`XR` paradigm. Streams are connected to data sources that produces data
over time. PhysioLab\ :sup:`XR` has the following hooks to connect to data sources and create streams:

1. **Network (LSL or ZMQ)**: connects to data streams broadcasted through the local area network.Streaming data over the data over
   the network is the quickest way to get started with PhysioLab\ :sup:`XR`, if you have
   a data source that supports one of the network APIs that PhysioLab\ :sup:`XR` supports.
   PhysioLab\ :sup:`XR` supports two popular network APIs for streaming experiment data: :ref:`LSL <stream over LSL>`
   and :ref:`ZMQ <stream over ZMQ>`. This is also the recommended way to stream data to and from other software such as
   game engines (e.g., :ref:`Unity <lsl data source in unity>`).

.. note::

    There are some caveats to consider when choosing between LSL and ZMQ.
    LSL is not suited for streams with a large number of channels (e.g., video), because each channel carries its own meta information and
    transmitting the meta information every frame creates a large overhead. For such streams, we recommend using `ZMQ <DataStreamAPI.html#using-zmq>`_.


2. **Python API**: for devices that has Python API, such as `brainflow <https://brainflow.org/>`_ for
   `OpenBCI devices <openbci.com>`_, you can write your own data source script and connect it to
   PhysioLab\ :sup:`XR` through the scripting interface. Check out :ref:`this page <stream using scripting interface>` on how to do it.

3. **Video and audio devices**: for devices recognized by the OS as webcams or microphones, they will automatically
   appear on the list of devices available to stream in PhysioLab\ :sup:`XR`.　:ref:`More information here <stream video, audio, screen capture>`.







