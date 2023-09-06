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
   appear on the list of devices available to stream in PhysioLab\ :sup:`XR`.　`More information here <DataStreamAPI.html#using-video-and-audio-devices>`_.


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


Using Video, Audio Input Devices, and Screen Capture
******************************************************

Video and audio input devices recognize by your OS are automatically detected and can be used as data sources.

Video Devices
----------------

Audio Input Devices
--------------------

Similar to the the video input devices, PhysioLab\ :sup:`XR` automatically detects the audio input devices
connected to your computer. Their name will be listed in the ``Add Stream dropdown``.
To add an audio input stream:

#. Click on the drop down of **Add Stream**  and select the audio device you want to add.
#. Once selected, a few new options settings will show up on the right of the dropdown.
#. Set the *sampling rate* (default: 8192), *frame/buffer* (default: 128), and *data type* (default: paInt16) of the audio device.
 - Please refer to the `PyAudio documentation <https://people.csail.mit.edu/hubert/pyaudio/docs/>`_ for more information about those parameters.
#. Click on **Add** button.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/datastreamapi-audio-interface.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>



Screen Capture
----------------


Refresh the list of devices
------------------------------

If you have connected a new device, you can refresh the list of devices by going to ``File > Settings > Video Device``,
and click ``Reload Video Devices``. Similarly, you can refresh the list of audio devices by going to
``File > Settings > Audio Device``, and click ``Reload Audio Devices``.

If your device is recognized by the OS, but not by PhysioLab\ :sup:`XR` and the data is not streamed correctly. Please
submit an issue `here <https://github.com/PhysioLabXR/PhysioLabXR/issues>`_.