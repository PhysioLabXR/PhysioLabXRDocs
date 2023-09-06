###############
Data Stream API
###############


Streams are the basic building blocks of any PhysioLab\ :sup:`XR` paradigm. Streams are connected to data sources that produces data
over time. PhysioLab\ :sup:`XR` has the following hooks to connect to data sources and create streams:

#. **Network**: connects to data streams broadcasted through the local area network.
   PhysioLab\ :sup:`XR` supports two popular network APIs for streaming experiment data: `LSL <DataStreamAPI.html#using-lsl>`_
   and `ZMQ <DataStreamAPI.html#using-zmq>`_
#. **Python API**: for devices that has Python API, such as `brainflow <https://brainflow.org/>`_ for
   `OpenBCI devices <openbci.com>`_, users can write their own data source script and connect it to PhysioLab\ :sup:`XR` through the scripting interface. `More information here <DataStreamAPI.html#write-your-data-source-script>`_.
#. **Video and audio devices**: for devices recognized by the OS as webcams or microphones, they will automatically
   appear on the list of devices available to stream in PhysioLab\ :sup:`XR`.　`More information here <DataStreamAPI.html#using-video-and-audio-devices>`_.


Using LSL
***********************

`LabStreamingLayer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_
is a network protocol for streaming time series data, mostly used for physiological experiments.
LSL has the following characteristics:

- Native support for many popular devices (`more information here on how to connect supported device with LSL <https://labstreaminglayer.readthedocs.io/info/supported_devices.html>`_).
- LSL is a cross-platform protocol, and has bindings for many programming languages. You can create your own data source script in your platform and PhysioLab\ :sup:`XR` will take care of the receiving end.
- LSL outlets can carry meta information of your stream such as sampling rate and channel names.

.. note::

    LSL is not suited for streams with a large number of channels (e.g., video), because each channel carries its own meta information and
    transmitting the meta information every frame creates a large overhead. For such streams, we recommend using `ZMQ <DataStreamAPI.html#using-zmq>`_.


Write your LSL Interface
--------------------------

LSL has API in many programming languages. For a more comprehensive guide, check out the `examples in LSL documentation <https://labstreaminglayer.readthedocs.io/dev/examples.html>`_.
Here, we will show a simple example of how to create an LSL outlet in Python.

To run this example, make sure you have Python installed. Then, install the physiolabxr package by running the following command in your terminal:

.. code-block:: bash

    pip install physiolabxr

Alternatively, you can just install the pylsl package by running the following command in your terminal:

.. code-block:: bash

    pip install pylsl

.. note::

    If you installed pylsl, you may need to add liblsl to your site-packages for it to work. Follow the instructions `here <https://github.com/labstreaminglayer/pylsl#liblsl-loading>`_.
    On the other hand, if you installed physiolabxr, the app will automatically add liblsl to your site-packages first time you run it.
    Use the command ``physiolabxr`` to run it from your terminal after pip installing it.


Now, create a new Python file and copy the following code:

.. code-block:: python

    import time
    from pylsl import StreamInfo, StreamOutlet

    # create a new stream info and outlet
    n_channels = 8
    info = StreamInfo('my_stream_name', 'my_stream_type', n_channels, 100, 'float32', 'my_stream_id')
    outlet = StreamOutlet(info)

    # send data
    while True:
        # make a new random 8-channel sample; this is converted into a pylsl.vectorf (the data type that is expected by push_sample)
        mysample = np.random.randn(n_channels).tolist()
        # now send it and wait for a bit
        outlet.push_sample(mysample)
        time.sleep(0.01)

Run the script with:

.. code-block:: bash

    python <your-file-name>.py

Open PhysioLab\ :sup:`XR` (download the App `here <index.html#download>`_ if you haven't already).
Type in the name of the stream you created in the script (``my_stream_name`` in the example above) in ``Add Stream``. Click on the ``Add`` button.
You should see the stream you created in the list of streams. Click on the |ico6| button to start streaming the data.


Using ZMQ
***********************

`ZMQ <https://zeromq.org/>`_ is a messaging library for exchanging data between applications. ZMQ has the following characteristics:

* ZMQ is a lightweight messaging library, and it is suited for streaming data with a large number of channels (e.g., video).
* Like `LSL <DataStreamAPI.html#using-lsl>`_, ZMQ is a cross-platform protocol, and has bindings for many programming languages.
* ZMQ supports many different socket patterns. PhysioLab\ :sup:`XR` supports the subscriber-publisher socket pattern, because this pattern is more scalable when the
  cardinality of data is high and less error-prone in case of either the publisher or subscriber process crashes. You can find a more detailed explanation of the socket patterns `here <https://zguide.zeromq.org/docs/chapter2/>`_.

Write your ZMQ Interface
--------------------------

Similar to LSL, ZMQ can be used in many programming languages. Here, we will show a simple example of how to create a ZMQ publisher
to send data in Python.

To run this example, make sure you have Python installed. Then, install the physiolabxr package by running the following command in your terminal:

.. code-block:: bash

    pip install physiolabxr

Alternatively, you can just install the pyzmq package by running the following command in your terminal:

.. code-block:: bash

    pip install pyzmq

Now, create a new Python file and copy the following code:

.. code-block:: python

    import time
    from collections import deque

    import numpy as np
    import zmq

    topic = "my_stream_name"
    srate = 120
    port = "5557"  # ZMQ port number

    c_channels = 3
    width = 64
    height = 64
    n_channels = c_channels * width * height

    context = zmq.Context()
    socket = context.socket(zmq.PUB)
    socket.bind("tcp://*:%s" % port)

    # next make an outlet
    print("now sending data...")
    send_times = deque(maxlen=srate * 10)
    start_time = time.time()
    sent_samples = 0
    while True:
        elapsed_time = time.time() - start_time
        required_samples = int(srate * elapsed_time) - sent_samples
        if required_samples > 0:
            samples = np.random.rand(required_samples * n_channels).reshape((required_samples, -1))
            samples = (samples * 255).astype(np.uint8)
            for sample_ix in range(required_samples):
                mysample = samples[sample_ix]
                socket.send_multipart([bytes(topic, "utf-8"), np.array(time.time()), mysample])  # send data
                send_times.append(time.time())
            sent_samples += required_samples
        # now send it and wait for a bit before trying again.
        time.sleep(0.01)
        if len(send_times) > 0:
            fps = len(send_times) / (np.max(send_times) - np.min(send_times))
            print("Send FPS is {0}".format(fps), end='\r')
        print(f'current timestamp is {time.time()}', end='\r', flush=True)


In this script, we created an ZMQ publisher that sends a random sample every 0.01 seconds. The sample is a 3-channel image of size 64x64.


.. note::

    Check :ref:`this page <ZMQInterface port numbers>` for how to choose ZMQ port so that it does not conflict with other applications.


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

.. |ico6| image:: /media/start.svg
   :width: 20px
   :height: 20px
