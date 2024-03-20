.. _stream over LSL:


##########################################
Stream data with Lab Streaming Layer (LSL)
##########################################

Lab Streaming Layer (LSL)
--------------------------

`LabStreamingLayer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_
is a network protocol for streaming time series data, mostly used for physiological experiments.
LSL has the following features:

- Native support for many popular devices (`check out the official documentation for more information on how to connect supported devices with LSL <https://labstreaminglayer.readthedocs.io/info/supported_devices.html>`_).
- LSL is a cross-platform protocol, and has bindings for many programming languages. You can create your own data source script in your platform and PhysioLab\ :sup:`XR` will take care of the receiving end.
- LSL outlets can carry meta information of your stream such as sampling rate and channel names.

LSL has a robust API, ported to many programming languages. For a more comprehensive guide, check out the `examples in the LSL documentation <https://labstreaminglayer.readthedocs.io/dev/examples.html>`_.
Here, we give a simple example of an LSL data source, in Python. We will show you how to generate arbitrary data arrays and broadcast them with LSL. You will then be able to listen to the stream in PhysioLab\ :sup:`XR`!

LSL in Python
^^^^^^^^^^^^^

In this LSL example, we will create a 100Hz, 8-channel stream. This means that there will be 100 data arrays per second, each with 8 useful elements (along with any desired metadata, such as timestamps and channel names).

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
            mysample = [rand() * 10 for _ in range(n_channels)] # generate random data
            outlet.push_sample(mysample) # and send it
        sent_samples += required_samples
        time.sleep(0.01) # now wait for a bit before trying again.

The script above will send a 100Hz stream with 8 channels of random data to PhysioLab\ :sup:`XR` through LSL.

2. Run the above script with:

.. code-block:: bash

    python <your-file-name>.py

You can find this script in PhysioLab\ :sup:`XR`'s GitHub repository `examples-WriteYourOwnDataSourceExamples <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/examples/WriteYourOwnDataSourceExamples/LSLExampleOutlet.py>`_.

Check out :ref:`this page <create lsl stream>` on how to create a stream to receive the data in PhysioLab\ :sup:`XR`.

LSL in Unity
^^^^^^^^^^^^

To use LSL with Unity, you need to install the LSL package in your Unity project:

Method 1: Use the PhysiolabXR-Unity-Package (Recommended):
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
We provide a preconfigured `Unity package <https://github.com/PhysioLabXR/Unity-PhysioLabXR-Plugin.git>`_ to manage LSL dependencies.
To install it, go to Window -> Package Manager, click on the "+" button, and select "Add package from git URL".
Then, paste the following URL: `https://github.com/PhysioLabXR/Unity-PhysioLabXR-Plugin.git <https://github.com/PhysioLabXR/Unity-PhysioLabXR-Plugin.git>`_.

For more information, go to its :ref:`documentation <LSLZMQUnityPackage>`.

Method 2: Install LSL package manually:
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Follow the instruction in `LSL4Unity <https://github.com/labstreaminglayer/LSL4Unity>`_.
We recommend using the *Install a package from a Git URL* to install the LSL package.

Once you have the LSL package installed in your Unity project,
please refer to the :ref:`docs for LSL data source in Unity <lsl data source in unity>` to learn how to create stream
data with LSL in Unity.

.. note::

    As an alternative to LSL, you can also use :ref:`ZMQ <stream ZMQ in Unity>` to stream data between Unity and
    PhysioLabXR, which is recommended if your data has a large number of channels such as Unity camera video. See this
    example on how to stream camera data from Unity to PhysioLab:sup:`XR <use zmq to stream camera image from Unity>`.