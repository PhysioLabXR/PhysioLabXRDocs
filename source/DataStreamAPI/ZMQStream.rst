.. _stream over ZMQ:

##############################
Stream data with ZeroMQ (ZMQ)
##############################

`ZMQ <https://zeromq.org/>`_ is a messaging library for exchanging data between applications.

Similar to :ref:`LSL <stream over LSL>`, ZMQ can be used in many programming languages. ZMQ has the following characteristics:

* ZMQ is a lightweight messaging library, and it is suited for streaming data with a large number of channels (e.g., video).
* Like :ref:`LSL <stream over LSL>`, ZMQ is a cross-platform protocol, and has bindings for many programming languages.
* ZMQ supports many different socket patterns. PhysioLab\ :sup:`XR` supports the
  `subscriber-publisher <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_ socket
  pattern, because this pattern is more scalable when the cardinality of data is high and less error-prone in case of
  either the publisher or subscriber process crashes. You can find a more detailed explanation of the socket patterns `here <https://zguide.zeromq.org/docs/chapter2/>`_.

You can find more technical details of ZMQ towards the :ref:`end of this page <zmq technicality>`.


Examples
************************

Here, we will show a simple example of how to create a ZMQ publisher
to send data in Python and Unity (C#). The following two section will show how to create ZMQ data source in Python and C# for your reference.
For this example, you can choose your preferred language to create the data source.


ZMQ in Python
-----------------------

In this section, we will show how to create a ZMQ publisher in Python, where we simulate a RGB video stream with 400x400 resolution and 15 fps.

To create the ZMQ data source in Python, first make sure you have Python installed.
Then, install the pyzmq package by running the following command in your terminal:

.. code-block:: bash

    pip install pyzmq


Now we are ready to run the example.

1. Create a new Python file and copy the following code into it. It will send image stream with random data to PhysioLab\ :sup:`XR` through ZMQ.


.. code-block:: python

    import time
    from collections import deque

    import numpy as np
    import zmq

    topic = "python_zmq_my_stream_name"  # name of the publisher's topic / stream name
    srate = 15  # we will send 15 frames per second
    port = "5557"  # ZMQ port number

    # we will send a random image of size 400x400 with 3 color channels
    c_channels = 3
    width = 400
    height = 400
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
                socket.send_multipart([bytes(topic, "utf-8"), np.array(time.time()), mysample])  # send frame in the order: topic, timestamp, data
                send_times.append(time.time())
            sent_samples += required_samples
        # now send it and wait for a bit before trying again.
        time.sleep(0.01)
        if len(send_times) > 0:
            fps = len(send_times) / (np.max(send_times) - np.min(send_times))
            print("Send FPS is {0}".format(fps), end='\r')
        print(f'current timestamp is {time.time()}', end='\r', flush=True)

This script will send a stream with random 400x400 image data with 3 color channels through ZMQ, on the port 5557.
PhysioLab\ :sup:`XR` can receive the stream by subscribing to the topic ``python_zmq_my_stream_name`` on port 5557.

2. Run the above script with:

.. code-block:: bash

    python <your-file-name>.py

You can find this script in PhysioLab\ :sup:`XR`'s GitHub repository `examples-WriteYourOwnDataSourceExamples <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/examples/WriteYourOwnDataSourceExamples/ZMQExamplePublisher.py>`_.

Check out :ref:`this page <create zmq stream>` on how to create a stream to receive the data in PhysioLab\ :sup:`XR`.


.. _zmq data source in unity:

ZMQ in Unity
-----------------------

Similarly, you have to install the ZMQ package in Unity. To do so, follow the instruction in `NuGetForUnity <https://github.com/GlitchEnzo/NuGetForUnity>`_.
Then you can search for ``ZMQ`` in the NuGet package manager and install it.

In the example project, you will find a object called ``ZMQPublisherController`` in the scene. This object contains a script called ``ZMQPublisherController.cs``.
You can find the script in the `LSL-ZMQ-4Unity-Example <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example/blob/main/Assets/Scripts/LSL/ZMQPublisherController.cs>`_ repository.


.. important::
    It is very important to have the ``ForceDotNet.Force()`` in your code,
    before you create a socket. Otherwise , the socket will fail to instantiate and Unity will freeze when exiting play mode.
    For example, if you create a socket in Unity's ``Start()``, call ``ForceDotNet.Force()``
    before creating the socket.
    See the example `here  <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example/blob/5d55a8b39be1938d8adf4a5c51de63625af0b46e/Assets/Scripts/ZMQ/ZMQPublisherController.cs#L49>`_ .

Check out :ref:`this page <create zmq stream>` on how to create a stream to receive the data in PhysioLab\ :sup:`XR`.


Stream Camera Data from Unity
-------------------------------------


In this example, we will show you how to write your own data source in Unity (C#) with the same functionality as the Python example above.
However, instead of sending random data in the ZMQ example above, we will send the camera data from Unity to PhysioLab\ :sup:`XR`.

To run both the LSL and ZMQ examples, you will need to have Unity installed. This project is made with Unity version 2023.3.10f1.

You can clone our example repository `here <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example>`_ and open it in Unity.

All the dependencies are already included in the project, so you can just open the project and run it.


Because ZMQ is a more generic messaging library compared to LSL, we will show you how to create a ZMQ publisher in Unity.


.. _zmq technicality:

Using ZMQ, things to note
**************************

PhysioLab\ :sup:`XR` uses `subscriber-publisher sockets <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_
for ZMQ streams, because this pattern is more scalable when the cardinality of data is high and less error-prone in case of
either the publisher or subscriber process crashes.


Unlike LSL, ZMQ is a messaging library for general use. We define a specific message format for PhysioLab\ :sup:`XR` to use.
When you are creating a ZMQ data source, make sure that you are sending the data in one of the following forms:

#. A frame containing two parts: the topic name, and the data. The topic name must be a string, and must match the stream name
   you set when adding the stream. It is necessary for ZMQ to route the message for `subscriber-publisher sockets <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_
   you have multiple `ZMQ publisher socket <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_
   connected to the same socket but publishing to different topics.

#. A frame containing three parts: the topic name, a timestamp, and the data.
   The timestamp must be 8-byte (64-bit) double or 4-byte (32-bit) float, indicating when the data was acquired. You
   can set the timestamp to any value that suits your application. Refer to :ref:`this page <time in physiolab>` for more information on time.


.. _ZMQInterface port numbers:

Port numbers
^^^^^^^^^^^^^

When choosing ports for ZMQ sockets, it is important to note that some ports are reserved for system use, and should not be used. For example, ports 0-1023 are typically reserved for system use.
Also, some ports are reserved for specific applications. For example, port 80 is reserved for HTTP.

Reserved ports


some ports are reserved by PhysioLab\ :sup:`XR`. These ports are listed below:

+------------------+---------------------------------------+
| Port             | Used by                               |
+==================+=======================================+
| 8000-8100        | :ref:`scripting <feature scripting>`  |
+------------------+---------------------------------------+
| 9980-9990        | :ref:`replay <feature replay>`        |
+------------------+---------------------------------------+

Default ports
^^^^^^^^^^^^^


When creating ZMQ streams from :ref:`Replay <replay stream interface>` and Scripting, their port numbers are chosen
from this list. You can always change these ports in the :ref:`user interface <replay stream interface port line edit>`.

+----------------------+-----------------------------------------------+
| Starting port number | Used by                                       |
+======================+===============================================+
| 11000                | :ref:`scripting outputs <feature scripting>`  |
+----------------------+-----------------------------------------------+
| 10000                | :ref:`replay streams <feature replay>`        |
+----------------------+-----------------------------------------------+


.. _zmq data types:

ZMQ Data types
^^^^^^^^^^^^^^^^


The ZMQ interface supports the following data types:

``uint8, uint16, uint32, uint64, int8, int16, int32, int64, float16, float32, float64``