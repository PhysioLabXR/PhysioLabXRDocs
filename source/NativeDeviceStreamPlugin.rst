.. _stream using native plugin:

############################################################
Native Device Stream Plugin
############################################################

PhysioLab\ :sup:`XR` offers an API interface that allows developers to easily integrate custom devices with the platform.
Once integrated, you can seamlessly include the new device in your multi-modal experiments, visualize its data streams,
record the data for offline analysis, and deploy machine learning models in real-time.

When to Create a Native Device Stream Plugin
---------------------------------------------
You should consider creating a native device stream plugin in the following cases:

- The device doesn’t have a Python API: If your device doesn’t natively support a Python SDK, a native plugin can bridge the gap, enabling smooth integration into PhysioLabXR.

- Optimize for performance: Native plugins can offer significant performance improvements over network-based integrations by minimizing overhead and enabling more direct access to the device's data.

- Portability and community sharing: Native plugins are more self-contained and portable, making it easier to share them with the community or use them across different setups.

Audience
--------
Creating a native device stream plugin is designed for advanced users. It requires a good understanding of the
device’s API, which is typically found on the device manufacturer's website. This document assumes that you are familiar
with your device's API.

This guide will focus on integrating your device with PhysioLab\ :sup:`XR`, covering both the technical aspects and
best practices.

High-Level Overview of Steps
----------------------------
At a high level, the following steps are involved in creating a native device stream plugin:

1. Create the Plugin Classes: You will need to create plugin classes for the device, including the device interface (<DeviceName>_Interface.py) and, optionally, a GUI class (<DeviceName>_Options.py) for more detailed control of the device.

2. Test the Device Interface: Verify that your interface works as expected with the device and ensuring it communicates correctly.

3. Integrate the Device with PhysioLab\ :sup:XR: This step involves registering your device within PhysioLabXR, ensuring that it is recognized by the platform and can be used in experiments.

4. Add Test Cases: Add unit tests to ensure the device interface behaves correctly under various conditions. This also simplifies maintenance and makes it easier for others to contribute or modify the plugin.

5. Make a Pull Request: Finally, contribute your plugin to the PhysioLabXR community by submitting a pull request. This allows others to benefit from your work and ensures long-term support through community engagement.

File and Directory Overview
---------------------------
Below is an overview of the relevant files and directories involved in creating and integrating a native device stream plugin.

Files you need to create:

* ``physiolabxr/interfaces/<DeviceName>/__init__.py``: This file should import and expose the core classes (<DeviceName>_Interface.py and optionally <DeviceName>_Options.py) that define your device interface and its control options.

* ``physiolabxr/interfaces/<DeviceName>/<DeviceName>_Interface.py``: This is the core device interface class, where the logic for communicating with the device is implemented.

* ``physiolabxr/interfaces/<DeviceName>/<DeviceName>_Options.py`` (optional): If you need to provide additional device-specific controls (e.g., setting a COM port or configuring device parameters), you can define these options here.

* ``physiolabxr/_ui/<DeviceName>_Options.ui`` (optional): If your device has a custom options class (<DeviceName>_Options.py), this .ui file defines the graphical interface for controlling those options.

* ``physiolabxr/_presets/DevicePresets/<DeviceName>.json``: This file contains metadata for your device, such as stream name, channel names, nominal sampling rate, and how to group the channels when plotting.

Files you should read and understand:

* ``physiolabxr/interfaces/DeviceInterface/DeviceInterface.py``: This is the base class from which your device interface will inherit. You will need to override most of its methods in your interface class. Understanding how each method here is essential for building your plugin.

* ``physiolabxr/threadings/DeviceWorker.py``: This file brigdes the device interface and rest of the platform. It is important to understand how threading works in PhysioLabXR, especially for high-performance or real-time applications.

Files that are helpful for a deeper understanding of data flow:

* ``physiolabxr/ui/DeviceWidget.py``: This file handles the user interface component of device management in the platform. It’s useful if your plugin involves UI interaction.

* ``physiolabxr/ui/BaseStreamWidget.py``: Understanding how streams are handled at the UI level will give you a better idea of how your device will appear and behave in the platform.


1. Create a device interface
*****************************

To create a device interface, you need to implement a class that inherits from the DeviceInterface class in ``physiolabxr/interfaces/DeviceInterface/DeviceInterface.py``.
This class will define the methods for starting, stopping, and processing the device stream, and device availability.
In our example, we will create a simple device interface for a dummy device named 'MyAwesomeDevice'.
You can replace 'MyAwesomeDevice' with the actual name of your device.

Files you need to create for this step:

* ``physiolabxr/interfaces/MyAwesomeDevice/__init__.py``
* ``physiolabxr/interfaces/MyAwesomeDevice/MyAwesomeDevice_Interface.py``

In the file, physiolabxr/interfaces/MyAwesomeDevice/__init__.py, include the following code to expose the device interface class:

.. code-block:: python

    from .MyAwesomeDevice_Interface import MyAwesomeDeviceInterface

Optionally, you can add a device options class to the device interface. In that case, you will need to add this line
``from .MyAwesomeDevice_Options import MyAwesomeDeviceOptions`` to the __init__.py file. Read more about it in the section `device options`_.

Now before you move on, carefully review the doctrings in the DeviceInterface class located in ``physiolabxr/interfaces/DeviceInterface/DeviceInterface.py``.
This will help you understand the key methods you need to implement for your custom device interface. The DeviceInterface class
provides the foundational structure that all device interfaces in PhysioLabXR must follow.

In this example, we will create a simple device interface for 'MyAwesomeDevice'.
The file we need to create is ``physiolabxr/interfaces/MyAwesomeDevice/MyAwesomeDevice_Interface.py``.
Again, if you are unsure about the methods you need to implement, you can refer to the docstrings in
the DeviceInterface class in ``physiolabxr/interfaces/DeviceInterface/DeviceInterface.py``.

.. code-block:: python

    from physiolabxr.interfaces.DeviceInterface.DeviceInterface import DeviceInterface
    from physiolabxr.third_party import MyAwesomeDeviceLibrary  # Import the library for the device, adjust according to the actual device library

    class MyAwesomeDeviceInterface(DeviceInterface):
        """A simple example interface for MyAwesomeDevice."""
        def __init__(self):
            super().__init__(_device_name='MyAwesomeDevice',
                             _device_type='eeg',  # Adjust according to the actual device type
                             device_nominal_sampling_rate=256,  # Adjust according to the actual device sampling rate
                             is_supports_device_availability=False)  # Adjust if your device supports device availability (read more about it in the section device availability)
            # You can initialize additional device-specific parameters here
            # In our example, we initialize a device instance from the device library. Adjust according to the actual device library
            self.my_device = MyAwesomeDeviceLibrary.MyAwesomeDevice()

        def start_stream(self):
            """Start the device stream.
            Initialize the connection or data retrieval mechanism for the device.
            """
            self.my_device.start_stream()  # Adjust according to the actual device library

        def process_frames(self):
            """
            Process and retrieve frames, timestamps, and messages from the device.

            Returns:
                A tuple of (frames, timestamps, messages).
            """
            print(f"Processing frames for {self._device_name}")
            frames, timestamps = self.my_device.get_frames()  # Adjust according to the actual device library
            messages = self.my_device.get_messages()  # Adjust according to the actual device library

            return frames, timestamps, messages

        def stop_stream(self):
            """Stop the device stream and clean up resources.

            Make sure to clean up resources and stop the device stream properly, such that the device is ready for the next start_stream call.
            """
            self.my_device.stop_stream()  # Adjust according to the actual device library

        def get_device_nominal_sampling_rate(self):
            """
            Get the nominal sampling rate of the device.

            Returns:
                The nominal sampling rate.
            """
            return self.device_nominal_sampling_rate

        def __del__(self):
            """Perform cleanup when the device interface is destroyed.
            """
            # we don't need to do anything here in this example, because we don't have any threads or processes that need to join or terminate
            pass


.. note::

    Note on timestamping.
    PhysioLab\ :sup:`XR` requires timestamps for each frame of data. Your device's timestamp should be in sync with
    the platform's timestamp. The platform uses the local clock to timestamp the data, the function in python
    ``time.monotonic()`` can be used to get the platform's timestamp. If your device has its own timestamp, you can
    convert it to the platform's timestamp using the difference between the device's timestamp and the platform's timestamp.
    We will check this again in the test cases.

.. note::

    Avoid blocking operations in ``process_frames``: The process_frames method in PhysioLab\ :sup:`XR` is called by
    default 1000 times per second. If the operations inside ``process_frames`` take too long, they can cause the entire
    application to lag, resulting in delayed UI updates and poor real-time performance. You should ensure that ``process_frames``
    completes quickly by using non-blocking calls or offloading time-consuming tasks to separate threads or processes.
    Check out the next section for more information on how to handle this.

1.1 (Optional) Use a separate process to communicate with the device
---------------------------------------------------------------------
In some cases, it may be difficult to create a Python binding for a device, or the device API may have blocking
operations that are hard to avoid. For example, the pull_data call of the device API doesn't return until new data is available,
In such scenarios, it’s helpful to move device communication to a separate process.
This process can be written in the language that the device supports and communicate with your device interface using
inter-process communication (IPC). Don't be discouraged if this sounds complex; handling IPC is straightforward, and it can
significantly improve the performance of your device interface.

For this purpose, we recommend using `ZeroMQ (ZMQ) <https://zeromq.org/>`_, a high-performance messaging library,
to handle the communication between the device process and the device interface in PhysioLab\ :sup:`XR`. ZMQ allows for
efficient non-blocking communication and is suitable for high-throughput real-time data.

Example: Using ZMQ for Inter-Process Communication
Here’s an example of how to set up a separate process for device communication using ZMQ:

#. Device Process: This process handles the actual communication with the device and sends data to the interface.

    .. code-block:: python

        import zmq
        import time

        def device_process(terminate_event, port):
            """Device process to communicate with the device and send data via ZMQ."""
            context = zmq.Context()
            socket = context.socket(zmq.PUB)  # Create a publisher socket
            socket.bind(f"tcp://*:{port}")  # Bind to a port

            while not terminate_event.is_set():
                # Simulate reading data from the device (replace with actual device communication)
                data = {"timestamp": time.time(), "frame": [1, 2, 3, 4]}  # Replace with actual device data
                # Send data as json
                socket.send_json(data)
                time.sleep(0.001)  # Simulate device's data generation rate (1ms per frame)

            socket.close()
            context.term()

#. Device Interface: This interface communicates with the device process using ZMQ and processes the data.

    .. code-block:: python

        import zmq
        from multiprocessing import Process, Event

        class MyAwesomeDeviceInterface(DeviceInterface):
            def __init__(self, _device_name='MyAwesomeDevice', _device_type='eeg', device_nominal_sampling_rate=256):
                super().__init__(_device_name, _device_type, device_nominal_sampling_rate)

                self.context = zmq.Context()
                self.socket = self.context.socket(zmq.SUB)  # Subscriber socket
                self.socket.setsockopt_string(zmq.SUBSCRIBE, '')  # Subscribe to all topics
                self.socket.connect("tcp://localhost:0")  # Bind to port 0 for an available random port
                self.port = self.socket.getsockopt(zmq.LAST_ENDPOINT).decode("utf-8").split(":")[-1]  # Get the randomly binded port number from the socket

                self.terminate_event = Event()
                self.device_process = None

            def start_stream(self):
                """Start the device stream in a separate process."""
                self.device_process = Process(target=device_process, args=(self.terminate_event, self.port))
                self.device_process.start()

            def process_frames(self):
                """Retrieve all available data from the device process via ZMQ in a non-blocking manner.
                This ensures that process_frames returns quickly, preventing application lag.

                Using the while loop to collect all available data to avoid the data lagging behind the real-time.
                """
                frames, timestamps, messages = [], [], []
                while True:  # Collect all available data
                    try:
                        data = self.socket.recv_pyobj(flags=zmq.NOBLOCK)  # Non-blocking receive
                        frames.append(data['frame'])
                        timestamps.append(data['timestamp'])
                    except zmq.Again:
                        # No more data available, break the loop
                        break

                return frames, timestamps, messages

            def stop_stream(self):
                """Stop the device process and clean up."""
                self.terminate_event.set()
                self.device_process.join()
                self.device_process = None

            def __del__(self):
                """Clean up ZMQ context and sockets.

                Note that you don't need to terminate the device process here, because this is handled in
                the stop_stream method. And stop_stream is called by the DeviceWorker before the interface is destroyed.
                """
                self.socket.close()
                self.context.term()


1.2 Test your interface with the device
------------------------------------

Before fully integrating your custom device interface with PhysioLab\ :sup:`XR`, it’s important to test
it independently. You can do this by adding a ``__main__`` block to your interface file, allowing you to run
the interface in isolation and ensure it interacts correctly with the device.

By running the interface in standalone mode, you can debug the connection, data streaming, and processing
logic without needing to integrate with the entire platform. This is especially useful for troubleshooting
device-specific issues.

Here’s an example of how to add a ``__main__`` block to the interface file for ``MyAwesomeDevice``:

.. code-block:: python

    if __name__ == "__main__":
        # Instantiate the device interface
        device_interface = MyAwesomeDeviceInterface()

        # Start the device stream
        device_interface.start_stream()

        try:
            # Continuously process frames from the device in a test loop
            for _ in range(100):  # Run for 100 iterations (or replace with a time-based loop)
                frames, timestamps, messages = device_interface.process_frames()
                if frames:
                    print(f"Frames: {frames}")
                    print(f"Timestamps: {timestamps}")
                if messages:
                    print(f"Messages: {messages}")

                time.sleep(0.1)  # Adjust sleep time to match expected data rate

        except KeyboardInterrupt:
            print("Test interrupted by user.")

        finally:
            # Stop the device stream and clean up resources
            device_interface.stop_stream()
            print("Device stream stopped and resources cleaned up.")

.. note::

    **Testing Repeated start_stream() and stop_stream() Calls: ** It's important to ensure that the device interface can
    be restarted after stopping. This verifies that your start_stream() and stop_stream() methods properly initialize
    and clean up the connection to the device, and that they can be called multiple times without issues.
    This helps confirm that the device can gracefully restart its data stream without requiring a full application restart.
    Make this works before moving on to the next step.

2. Integrate your device interface with PhysioLab\ :sup:`XR`
**********************************************************

Once your device interface is working as expected and passes standalone tests, integrating it with PhysioLab\ :sup:`XR`
is straightforward. All you need to do is register your device by creating a device preset JSON file.

The preset JSON file contains metadata about your device, including its name,
nominal sampling rate, and how to group the channels for plotting.

Create a device preset JSON file: ``physiolabxr/_presets/DevicePresets/<DeviceName>.json``. For our example,
we will create a preset file ``physiolabxr/_presets/DevicePresets/MyAwesomeDevice.json``.

Below is an example of what the JSON preset for 'MyAwesomeDevice' could look like. Make sure to adjust the parameters
based on your device’s specifications:

.. code-block:: json

    {
      "StreamName": "MyAwesomeDevice",
      "NominalSamplingRate": 256,
      "ChannelNames": [
        "EEGCh1",
        "EEGCh2",
        "EEGCh3",
        "EEGCh4",
        "AuxCh1",
        "AuxCh2"
        "GyroX",
        "GyroY",
        "GyroZ"
      ],
      "GroupInfo": [3, 5],
      "DataType": "float32"
    }

The GroupInfo field specifies how to group the channels for plotting. In this example, channel 0-3 are EEGs and are grouped together
in one plot. Then channel 4 and 5 are grouped together, and the rest gryo channels are plotted in another group.
This grouping is useful for visualizing lineplots for multi-channel data when the values of different groups are vastly different.


2.1 (Optional) Device availability
----------------------------------

In PhysioLab\ :sup:`XR`, devices can optionally support availability checks to determine if they are ready to stream data.
If your device doesn't support availability checks, in your interface class, just set is_supports_device_availability=False and assume the
device is always ready to stream.

If your device can dynamically report its availability status, you can implement this feature by setting the
is_supports_device_availability attribute to True in your device interface, like the following:

.. code-block:: python

    class MyAwesomeDeviceInterface(DeviceInterface):
        def __init__(self):
            super().__init__(_device_name='MyAwesomeDevice',
                             _device_type='eeg',
                             device_nominal_sampling_rate=256,
                             is_supports_device_availability=True)  # Set to True if your device supports availability checks
            # ...


When availability is supported, you must implement the is_stream_available() method in your device interface.
This method will be called at regular intervals (every 2 seconds) to check if the device is ready to stream.
If the device is available, this method should return True; otherwise, it should return False.

.. note::

    This method runs frequently on the main thread, so it must not block. If you're using communication mechanisms
    like ZMQ, ensure non-blocking calls are used (e.g., zmq.NOBLOCK).

.. _device options:

2.2 (Optional) Create a device options class
--------------------------------------------

If your device requires additional configuration or user interaction (e.g., setting COM ports, adjusting
sampling rates), you can implement a custom Device Options class. This class allows users to manage device-specific
settings via a user interface integrated with PhysioLab\ :sup:`XR`.

The Device Options class is a subclass of `QWidget <https://doc.qt.io/qtforpython-5/PySide2/QtWidgets/QWidget.html>`_
in `PyQT <https://doc.qt.io/qtforpython-6/>`_, where you define the UI components that users will interact with.
You can also create a corresponding ``.ui`` file using a `Qt Designer tool <https://doc.qt.io/qt-6/qtdesigner-manual.html>`_
for defining the graphical layout.

For an example of how to implement device options, you can refer to the DSI device implementation in the PhysioLabXR codebase:

* Python class: ``physiolabxr/interfaces/DeviceInterface/DSI24/DSI24_Options.py``

* UI layout file: ``physiolabxr/_ui/DSI24_Options.ui``

By providing a device options class, you give users enhanced control over how the device interacts with the platform,
making it easier to customize device settings for different experiment setups.

Add test cases
**************

To ensure that your device interface behaves correctly, you should add your test cases in the tests/ directory to verify
that your device integration works as expected.

This section will expand over time to include more examples and cover additional testing scenarios, but the following
basic test areas should be implemented to ensure a robust interface:

* Check Device Connection: Test if your device can establish a connection during the start_stream() call and whether the connection can be re-established after the stream is stopped using stop_stream(). This ensures that the connection lifecycle behaves as expected.

* Check Reading Data: Verify that your interface correctly reads data from the device. Ensure that data frames are retrieved properly, match the expected format, and handle any potential errors in data acquisition (e.g., device disconnects).

* Check Timestamps: Test if the data is correctly synchronized with the platform’s timestamp system. Ensure that each frame of data is accompanied by a corresponding timestamp that accurately reflects the time of data capture.

* Test Integration with PhysioLab\ :sup:XR: This is a GUI-based test to check the full integration of your device with the platform. Verify that your device appears in the PhysioLabXR interface, can be selected in an experiment, and streams data correctly under various conditions. This also includes testing the real-time visualization and recording of data.

Make a pull request to share your plugin with the community
-----------------------------------------------------------

Once you’ve successfully implemented and tested your device interface, consider sharing it with the community by
submitting a pull request (PR) to the PhysioLab\ :sup:XR repository. This helps other users benefit from your work and
allows your device plugin to be maintained and improved by the broader community.

Please reivew the :ref:`Contribute to PhysioLabXR <contribute>` guide for detailed instructions on how to submit a pull request.

Here's a checklist before submitting a pull request for your device plugin:
#. Device Interface: Ensure your device interface works correctly and passes all standalone tests.
#. Device Options (optional): If you have added a device options class, verify it works as expected and integrates properly with the UI.
#. Preset File: Add the JSON preset file for your device in the physiolabxr/_presets/DevicePresets/ directory.
#. Test Cases: Include test cases in the tests/ directory to cover key functionality like connection, data reading, timestamps, and platform integration.
#. Documentation: Add or update documentation (if necessary) to help other users understand how to use your plugin.
#. Code Style: Ensure that your code adheres to the project’s :ref:`coding standards <repository-standards>`.

Once your pull request is submitted, the community and maintainers will review your plugin, and after approval, it
will be merged into the official PhysioLabXR codebase.

And that's it! You have successfully created and shared a native device stream plugin for PhysioLab\ :sup:`XR`.
Thank you for contributing to the project!