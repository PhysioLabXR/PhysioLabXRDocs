.. _stream over LSL:


##########################################
Stream data with Lab Streaming Layer (LSL)
##########################################

Lab Streaming Layer (LSL)
--------------------------

`LabStreamingLayer (LSL) <https://labstreaminglayer.readthedocs.io/info/intro.html>`_
is a network protocol for streaming time series data, mostly used for physiological experiments.
LSL has the following characteristics:

- Native support for many popular devices (`more information here on how to connect supported device with LSL <https://labstreaminglayer.readthedocs.io/info/supported_devices.html>`_).
- LSL is a cross-platform protocol, and has bindings for many programming languages. You can create your own data source script in your platform and PhysioLab\ :sup:`XR` will take care of the receiving end.
- LSL outlets can carry meta information of your stream such as sampling rate and channel names.

LSL has API in many programming languages. For a more comprehensive guide, check out the `examples in the LSL documentation <https://labstreaminglayer.readthedocs.io/dev/examples.html>`_.
Here, we give two simple example for Python and Unity, respectively. We will show how to stream data between PhysioLabXR and your own data source in Python and Unity (C#), via LSL.


LSL in Python
^^^^^^^^^^^^^

In this LSL example, we will create a stream with 8 channels and 100 Hz sampling rate.

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

Check out :ref:`this page <create lsl stream>` on how to create a stream to receive the data in PhysioLab\ :sup:`XR`.


.. _lsl data source in unity:

LSL in Unity
^^^^^^^^^^^^

In your project, you will need to install the LSL package in Unity. To do so,
follow the instruction in `LSL4Unity <https://github.com/labstreaminglayer/LSL4Unity>`_.
We recommend using the `Install a package from a Git URL <https://docs.unity3d.com/Manual/upm-ui-giturl.html>`_ to install the LSL package.

Here we provide an example Unity project with LSL installed. In the example project,
you will find a object called ``LSLOutletController`` in the scene. This object contains a
script called ``LSLOutletController.cs``.
You can find the script in the `LSL-ZMQ-4Unity-Example <https://github.com/HaowenWeiJohn/LSL-ZMQ-4Unity-Example/blob/main/Assets/Scripts/LSL/LSLOutletController.cs>`_ repository.

Check out :ref:`this page <create lsl stream>` on how to create a stream to receive the data in PhysioLab\ :sup:`XR`.




