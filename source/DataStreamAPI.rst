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



Introduction
------------



Lab Streaming Layer (LSL)
^^^^^^^^^^^^^^^^^^^^^^^^^

`LabStreamingLayer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_
is a network protocol for streaming time series data, mostly used for physiological experiments.
LSL has the following characteristics:

- Native support for many popular devices (`more information here on how to connect supported device with LSL <https://labstreaminglayer.readthedocs.io/info/supported_devices.html>`_).
- LSL is a cross-platform protocol, and has bindings for many programming languages. You can create your own data source script in your platform and PhysioLab\ :sup:`XR` will take care of the receiving end.
- LSL outlets can carry meta information of your stream such as sampling rate and channel names.

LSL has API in many programming languages. For a more comprehensive guide, check out the `examples in LSL documentation <https://labstreaminglayer.readthedocs.io/dev/examples.html>`_.
Here, we will show two simple example of how to create an LSL outlet in Python and Unity.


.. note::

    LSL is not suited for streams with a large number of channels (e.g., video), because each channel carries its own meta information and
    transmitting the meta information every frame creates a large overhead. For such streams, we recommend using `ZMQ <DataStreamAPI.html#using-zmq>`_.



.. _zmq data source:
ZeroMQ (ZMQ)
^^^^^^^^^^^^

`ZMQ <https://zeromq.org/>`_ is a messaging library for exchanging data between applications. ZMQ has the following characteristics:

* ZMQ is a lightweight messaging library, and it is suited for streaming data with a large number of channels (e.g., video).
* Like `LSL <DataStreamAPI.html#using-lsl>`_, ZMQ is a cross-platform protocol, and has bindings for many programming languages.
* ZMQ supports many different socket patterns. PhysioLab\ :sup:`XR` supports the
  `subscriber-publisher <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_ socket
  pattern, because this pattern is more scalable when the cardinality of data is high and less error-prone in case of
  either the publisher or subscriber process crashes. You can find a more detailed explanation of the socket patterns `here <https://zguide.zeromq.org/docs/chapter2/>`_.



Similar to LSL, ZMQ can be used in many programming languages. Here, we will show a simple example of how to create a ZMQ publisher
to send data in Python and Unity (C#). The following two section will show how to create ZMQ data source in Python and C# for your reference.
For this example, you can choose your preferred language to create the data source.



Write Your Own Data Source In Python
------------------------------------

In this section, we will show how to create a ZMQ publisher in Python.
The LSL example allows you to create a stream with 8 channels and 100 Hz sampling rate.
In the ZMQ example, we will simulate a RGB video stream with 400x400 resolution and 15 fps.

LSL in Python
^^^^^^^^^

To run the example, make sure you have Python installed. Then, install the physiolabxr package by running the following command in your terminal:

.. code-block:: bash

    pip install physiolabxr

Alternatively, you can just install the pylsl package by running the following command in your terminal:

.. code-block:: bash

    pip install pylsl

.. note::

    If you installed pylsl, you may need to add liblsl to your site-packages for it to work. Follow the instructions `here <https://github.com/labstreaminglayer/pylsl#liblsl-loading>`_.
    On the other hand, if you installed physiolabxr, the app will automatically add liblsl to your site-packages first time you run it.
    Use the command ``physiolabxr`` to run it from your terminal after pip installing it.


Now we are ready to run the example.

1. Create a new Python file and copy the following code into it:

.. code-block:: python

    import time
    from pylsl import StreamInfo, StreamOutlet, local_clock
    from random import random as rand

    # create a new stream info and outlet

    stream_name = 'python_lsl_my_stream_name'

    n_channels = 8
    # using the local_clock() to track elapsed time
    start_time = local_clock()
    # track how many samples we have sent
    sent_samples = 0
    # set the sampling rate to 100 Hz
    nominal_sampling_rate = 100

    info = StreamInfo('stream_name', 'my_stream_type', n_channels, nominal_sampling_rate, 'float32',
                      'my_stream_id')
    outlet = StreamOutlet(info)

    # send data
    while True:
        # calculate how many samples we need to send since the last call
        elapsed_time = local_clock() - start_time
        required_samples = int(nominal_sampling_rate * elapsed_time) - sent_samples
        for sample_ix in range(required_samples):
            mysample = [rand() * 10 for _ in range(n_channels)]
            # now send it
            outlet.push_sample(mysample)
        sent_samples += required_samples
        # now send it and wait for a bit before trying again.
        time.sleep(0.01)

Th script above will send a stream with 8 channels of random data to PhysioLab\ :sup:`XR` through LSL.

2. Run the above script with:

.. code-block:: bash

    python <your-file-name>.py

You can find this script in PhysioLab\ :sup:`XR`'s GitHub repository `examples-WriteYourOwnDataSourceExamples <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/examples/WriteYourOwnDataSourceExamples/LSLExampleOutlet.py>`_.


ZMQ in Python
^^^^^^^^^

The following code will send image stream with random data to PhysioLab\ :sup:`XR` through ZMQ.

To create the ZMQ data source in Python, first make sure you have Python installed.
Then, install the pyzmq package by running the following command in your terminal:

.. code-block:: bash

    pip install pyzmq


Now we are ready to run the example.

1. Create a new Python file and copy the following code into it:

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

The script above will send a stream with random image data with 3 color channels and size 400x400 to PhysioLab\ :sup:`XR` through ZMQ.

2. Run the above script with:

.. code-block:: bash

    python <your-file-name>.py

You can find this script in PhysioLab\ :sup:`XR`'s GitHub repository `examples-WriteYourOwnDataSourceExamples <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/examples/WriteYourOwnDataSourceExamples/ZMQExamplePublisher.py>`_.


Write Your Own Data Source In Unity (C#)
----------------------------------------

In this example, we will show you how to write your own data source in Unity (C#) with the same functionality as the Python example above.
However, instead of sending random data in the ZMQ example above, we will send the camera data from Unity to PhysioLab\ :sup:`XR`.

To run both the LSL and ZMQ examples, you will need to have Unity installed. In our case, we are using Unity 2023.3.10f1.

You can clone our example repository `here <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example>`_ and open it in Unity.

All the dependencies are already included in the project, so you can just open the project and run it.



LSL in Unity
^^^^^^^^^

If you are starting a new project, you will need to install the LSL package in Unity. To do so, follow the instruction in `LSL4Unity <https://github.com/labstreaminglayer/LSL4Unity>`_.
We recommend using the `Install a package from a Git URL <https://docs.unity3d.com/Manual/upm-ui-giturl.html>`_ to install the LSL package.


However, if you are using our example project, you can skip this step.

In the example project, you will find a object called ``LSLOutletController`` in the scene. This object contains a script called ``LSLOutletController.cs``.
You can find the script in the `LSL-ZMQ-4Unity-Example <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example/blob/main/Assets/Scripts/LSL/LSLOutletController.cs>`_ repository.


.. _zmq data source in unity:
ZMQ in Unity
^^^^^^^^^

Similarly, you have to install the ZMQ package in Unity. To do so, follow the instruction in `NuGetForUnity <https://github.com/GlitchEnzo/NuGetForUnity>`_.
Then you can search for ``ZMQ`` in the NuGet package manager and install it.

In the example project, you will find a object called ``ZMQPublisherController`` in the scene. This object contains a script called ``ZMQPublisherController.cs``.
You can find the script in the `LSL-ZMQ-4Unity-Example <LSL-ZMQ-4Unity-Example <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example/blob/main/Assets/Scripts/LSL/ZMQPublisherController.cs>`_ repository.


.. important::
    It is very important to note that you must have the ``ForceDotNet.Force()`` line in your code;
    before you create a socket. Otherwise, the socket would not be created and the application will freeze on exit.
    The detailed implementation can be found in `This Line <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example/blob/5d55a8b39be1938d8adf4a5c51de63625af0b46e/Assets/Scripts/ZMQ/ZMQPublisherController.cs#L49>`_ .







Visualize Your Data
-------------------

Now that you have a data source running, you can visualize your data in PhysioLab\ :sup:`XR` by following the steps below:

Visualize LSL Data from Python Or Unity
^^^^^^^^^^^^^^^^^^

1. Open PhysioLab\ :sup:`XR` (download the App `here <index.html#download>`_ if you haven't already).

2. Type in the name of the stream you created in the script (e.g. ``python_lsl_my_stream_name`` or ``unity_lsl_my_stream_name`` in the example above) in ``Add Stream Widget``.

3. Select ``LSL`` as the stream type in the dropdown just right where you put in the stream name.

4. Click on the ``Add`` button, a stream widget will be added to the main window. The widget will have |stream_available| icon on the bottom. This means that the stream is available on the network. But we are not receiving and plotting any data yet.

5. Click on the |ico6| button to start streaming the data.

6. You will be prompted to auto-set the number of channels to what's being streamed from your script above. This is because the default number of channels is 1 for newly added stream widget. Click on ``Yes`` to auto-set the number of channels.

7. The |stream_available| icon will change to |stream_active| icon. You will see the data being plotted in line chart (:ref:`explore other visualization method <feature visualization>`).


Visualize ZMQ Data from Python Or Unity
^^^^^^^^^^^^^^^^^^


In both Python and C# scripts, we created an ZMQ publisher socket that sends a random frames with 400 × 400 × 3 (480000) channels,
as if it is a video stream that has a height of 400, width of 400, and 3 color channels.
The publisher sends 15 frames per second. The Python script sends random images while the Unity script sends images from a Unity camera.

.. important::
   A ZMQ frame must have data in the following order: topic, timestamp, data. The topic must be a string, and the timestamp
   must be a 8-byte (64-bit) double or 4-byte (32-bit) float. The data can be one of the supported types listed :ref:`here <zmq data types>`.


1. Now return to PhysioLab\ :sup:`XR` (download the App `here <index.html#download>`_ if you haven't already). In the ``Add Stream`` line edit, type in the name of the stream you created in the script (e.g. ``python_zmq_my_stream_name`` or ``unity_zmq_my_stream_name`` in the example above).

2. Select ``ZMQ`` as the stream type in the dropdown just right where you put in the stream name.

3. After ``ZMQ`` is selected, the ``port number`` line edit will show up. Type in the port number you used in the script (``5557`` in the example above).

4. Click on the ``Add`` button. A stream widget will be added to the main window. Unlike LSL stream, ZMQ stream will not have |stream_available| icon on the bottom. This is because ZMQ does not have a mechanism to check if the stream is available on the network.

5. Now click on the |ico6| button.

6. You will be prompted to auto-set the number of channels to what's being streamed from your script above. This is because the default number of channels is 1 for newly added stream widget. Click on ``Yes`` to auto-set the number of channels.

7. Because the number of channels we set is 12288, greater than the maximum number of channels that can be plotted in a line chart. It will automatically switch to image plot (:ref:`explore other visualization method <feature visualization>`). You need to set the ``height`` and ``width`` and other image meta info in its to see the image.

8. Click on ``...`` button to open the plot settings. Set the ``width`` and ``height`` to 64, and ``Image`` to "rgb".

9. Return to the plot widget, move your cursor to the lower left of the plot, click the *[A]* button that shows up to have the image auto-scale to fit the window.


.. note::

    Check :ref:`this page <ZMQInterface port numbers>` for how to choose ZMQ port so that it does not conflict with other applications.





Write your data source using :ref:`Scripting Interface <feature scripting>`
----------------------------------------------------------------------------

You can create your own data source using the :ref:`Scripting Interface <feature scripting>` in Physiolab\ :sup:`XR`.
:ref:`This section <tutorial how to use scripting for device>` in Scripting gives detailed examples on how to write your own data source using the third party device driver API and the Physiolab\ :sup:`XR` scripting interface.










Using Video, Audio Input Devices, and Screen Capture
----------------------------------------------------

Video and audio input devices recognize by your OS are automatically detected and can be used as data sources.


.. _datastreamapi video devices:

Video Devices
----------------
PhysioLab\ :sup:`XR` automatically detects the video input devices
connected to your computer. Their name will be listed in the ``Add Stream`` dropdown as ``Camera <camera id>``.
To add an video input stream:

#. Click on the drop down of **Add Stream**  and select the video device you want to add.
#. Click on **Add** button.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/video_device.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>


Audio Input Devices
--------------------

Similar to the the video input devices, PhysioLab\ :sup:`XR` automatically detects the audio input devices
connected to your computer. Their name will be listed in the ``Add Stream dropdown``.
To add an audio input stream:

#. Click on the drop down of **Add Stream**  and select the audio device you want to add.
#. Once selected, a few new options settings will show up on the right of the dropdown.
#. Set the *sampling rate* (default: 8192), *frame/buffer* (default: 128), and *data type* (default: paInt16) of the audio device.
#. Click on **Add** button.

.. note::
    Please refer to the `PyAudio documentation <https://people.csail.mit.edu/hubert/pyaudio/docs/>`_ for more information about parameters for audio interface.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video-audio" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/datastreamapi-audio-interface.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>



Screen Capture
----------------
Similar to the the video input devices, PhysioLab\ :sup:`XR` supports streaming screen capture. The name of the screen capture stream will be ``monitor 0`` as default.
To add an video input stream:

#. Click on the drop down of **Add Stream**  and select ``monitor 0``.
#. Click on **Add** button.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/screencap.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Refresh the list of devices
------------------------------

If you have connected a new device, you can refresh the list of devices by going to ``File > Settings > Video Device``,
and click ``Reload Video Devices``. Similarly, you can refresh the list of audio devices by going to
``File > Settings > Audio Device``, and click ``Reload Audio Devices``.

If your device is recognized by the OS, but not by PhysioLab\ :sup:`XR` and the data is not streamed correctly. Please
submit an issue `here <https://github.com/PhysioLabXR/PhysioLabXR/issues>`_.





.. raw:: html

    <script>
        // Function to check if a video is visible in the viewport
        function isVideoVisible(videoId) {
            var video = document.getElementById(videoId);
            var rect = video.getBoundingClientRect();
            return rect.top >= 0 && rect.bottom <= window.innerHeight;
        }

        // Function to start the video if it is visible
        function checkAndPlayVideo(videoId) {
            var video = document.getElementById(videoId);
            if (isVideoVisible(videoId) && video.paused) {
                video.play();
            }
        }

        // Attach an event listener to check when a video is in the viewport
        window.addEventListener("scroll", function() {
            checkAndPlayVideo("autoplay-video-audio");
            checkAndPlayVideo("autoplay-video-lsl");
            checkAndPlayVideo("autoplay-video-zmq");
            // Add more videos as needed, using their respective video IDs
        });
    </script>



.. |ico6| image:: /media/start.svg
   :width: 20px
   :height: 20px


.. |stream_available| image:: /media/streamwidget_stream_available.svg
   :width: 20px
   :height: 20px

.. |stream_active| image:: /media/streamwidget_stream_viz_active.svg
   :width: 20px
   :height: 20px