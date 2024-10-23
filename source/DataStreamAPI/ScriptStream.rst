
.. _stream using scripting interface:

####################################################
Stream Custom Data source using Scripting Interface
####################################################

Most of the sensors and devices connect to your computer via USB or Bluetooth, and you can print the data to the console using the SDK provided by the device manufacturer.
This is a good way to test the device and get familiar with the data.
However, if you want to use the data in your experiment and synchronize it with other data streams, you will need to use **time.time()** to get the timestamp of the data and write the data to a file.
This could be a tedious process and you will need to do it for every device you want to use.

The :ref:`Scripting Interface <feature scripting>` in Physiolab\ :sup:`XR` can be used as a agent between the device driver and LSL or ZMQ streams.
In general, you can initialize third party device driver for the target device in the **init()** function and get the data in the **loop()** function and boardcast it to LSL or ZMQ streams.


.. _openbci cyton stream:

OpenBCI Cyton 8 channels EEG device
************************************

In this section, we will show you how to create a custom data source for `OpenBCI Cyton 8 channels EEG device <https://docs.openbci.com/GettingStarted/Boards/CytonGS/>`_ using the scripting interface and
the third party open source library `BrainFlow <https://brainflow.readthedocs.io/en/stable/SupportedBoards.html#cyton>`_.



1. import libraries

.. code-block:: python

    import time

    import brainflow
    import pylsl
    from brainflow.board_shim import BoardShim, BrainFlowInputParams
    from physiolabxr.scripting.RenaScript import RenaScript


2. Initialize the device interface in the **init()** function

.. code-block:: python

    # Start will be called once when the run button is hit.
    def init(self):
        # check if the parameters are set

        if "serial_port" not in self.params: # check
            while True:
                print("serial_port is not set. Please set it in the parameters tab (e.g. COM3)")
                time.sleep(1)
        else:
            if type(self.params["serial_port"]) is not str:
                while True:
                    print("serial_port should be a string (e.g. COM3)")
                    time.sleep(1)



        print("serial_port: ", self.params["serial_port"])

        # try init board
        self.brinflow_input_params = BrainFlowInputParams()

        # assign serial port from params to brainflow input params
        self.brinflow_input_params.serial_port = self.params["serial_port"]

        self.brinflow_input_params.ip_port = 0
        self.brinflow_input_params.mac_address = ''
        self.brinflow_input_params.other_info = ''
        self.brinflow_input_params.serial_number = ''
        self.brinflow_input_params.ip_address = ''
        self.brinflow_input_params.ip_protocol = 0
        self.brinflow_input_params.timeout = 0
        self.brinflow_input_params.file = ''

        # set board id to Cyton 8-channel (0)
        self.board_id = 0 # Cyton 8-channel

        try:
            self.board = BoardShim(self.board_id, self.brinflow_input_params)
            self.board.prepare_session()
            self.board.start_stream(45000, '') # 45000 is the default and recommended ring buffer size
            print("OpenBCI Cyton 8 Channels. Sensor Start.")
        except brainflow.board_shim.BrainFlowError:
            while True:
                print('Board is not ready. Start Fild. Please check the serial port and try again.')
                time.sleep(1)

The **BoardShim** class is the main class to handle the board. The first parameter is the board id, which is 0 for Cyton 8-channel. The second parameter is the **BrainFlowInputParams** class, which is used to set the serial port.
The detailed documentation for those classes can be found in the `BrainFlow documentation <https://brainflow.readthedocs.io/en/stable/>`_.

Instead of hard coding the serial port number in the script, we can use the **self.params** dictionary to set the serial port in the **Parameters** tab. The **init()** function will be called when the run button is hit. If the serial port is not set, the script will wait until the serial port is set in the **Parameters** tab.
If the serial port is set, the script will try to initialize the board. If the board is not ready, the script will stuck in the while loop and the user will need to stop the script and set the serial port again.

    The **serial_port** parameter should be a string, e.g. COM3 for Windows and /dev/ttyUSB0 for Linux. You can find the serial port number in the device manager in Windows and using the **ls /dev/ttyUSB*** command in Linux.

3. Get the data in the **loop()** function and send it to LSL stream

.. code-block:: python

    # loop is called <Run Frequency> times per second
    def loop(self):
        timestamp_channel = self.board.get_timestamp_channel(0)
        eeg_channels = self.board.get_eeg_channels(0)
        # print(timestamp_channel)
        # print(eeg_channels)

        data = self.board.get_board_data()

        timestamps = data[timestamp_channel]
        data = data[eeg_channels]

        absolute_time_to_lsl_time_offset = time.time() - pylsl.local_clock()
        timestamps = timestamps - absolute_time_to_lsl_time_offset # remove the offset between lsl clock and absolute time
        self.set_output(stream_name="OpenBCICyton8Channels", data=data, timestamp=timestamps)

In this example, the **get_timestamp_channel()** and **get_eeg_channels()** functions are used to get the index of timestamp channel and EEG channels.
The **get_board_data()** function is used to get all the dataframes from the buffer. The shape of the data is a 2D array with the first dimension being the number of frames and the second dimension being the number of channels.
OpenBCI Cython 8 channels use the unix timestamp, so we need to remove the offset between the unix timestamp and the LSL clock.
The **set_output()** function is used to boardcast the data to the LSL stream with the name **OpenBCICyton8Channels**.



    The complete script can be downloaded from `PhysioLabXROpenBCICyton8ChannelsScript.py <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/scripting/Examples/PhysioLabXR_P300Speller_Demo/PhysioLabXROpenBCICyton8ChannelsScript.py>`_.

To run the script, please refer to :ref:`Start OpenBCI Cyton-8-Channels <start the openbci cyton 8 channels board from physiolabxr scripting interface using physiolabxropenbcicyton8channelsscript.py>`
in the :ref:`P300 Speller Tutorial<tutorial p300>`.


