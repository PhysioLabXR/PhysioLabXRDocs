.. _Wearable Sensing Stream Old:

######################################################
Stream data from Wearable Sensing's DSI-24 Via Script
######################################################

1. Open PhysioLab\ :sup:`XR`.

2. Go to `Script Tab <Scripting.html>`_.

3. Click *Locate* and find the WearableSensingScript.py.

The script can be downloaded from `WearableSensingScript.py <https://github.com/LommyTea/WearableSensingXPhysioLabXR/blob/main/WearableSensingScript.py>`_.

.. code-block:: python

    from pylsl import local_clock
    from physiolabxr.scripting.RenaScript import RenaScript
    from physiolabxr.thirdparty.WearableSensing.DSI_py3 import *
    import numpy as np
    import sys
    from physiolabxr.utils.buffers import DataBuffer

    #Creating a data buffer with the DataBuffer class
    data_buffer = DataBuffer()

    is_first_time = True
    time_offset = 0  # time offset for the first packet to the local_clock()
    @SampleCallback
    def example_sample_callback_signals(headsetPtr, packetTime, userData):
        global is_first_time
        global time_offset
        global dsi24_data_socket

        # This function is called when a new packet is received
        h = Headset(headsetPtr)
        new_data = np.array(['%+08.2f' % (ch.GetSignal()) for ch in h.Channels()])
        new_data = new_data.reshape(24, 1)
        new_data = new_data[[9, 10, 3, 2, 4, 17, 18, 7, 1, 5, 11, 22, 12, 21, 8, 0, 6, 13, 14, 20, 23, 19, 16, 15], :]

        # Calculate the time offset on the first packet
        if is_first_time:
            time_offset = local_clock() - float(packetTime)
            is_first_time = False

        t = [float(packetTime) + time_offset]
        if new_data.shape[1] != len(t):
            print('Data and timestamp mismatch')
            print(new_data.shape)
            print(len(t))

        #Create a dictionary with the stream name, data, and timestamps
        new_data_dict = {
            'stream_name': 'DSI-24',
            'frames': new_data,
            'timestamps': t
        }
        #Update the data buffer with the new data
        data_buffer.update_buffer(new_data_dict)
    class DSI24(RenaScript):
        def __init__(self, *args, **kwargs):
            """
            Please do not edit this function
            """
            super().__init__(*args, **kwargs)

        def init(self, arg = ''):
            #Create a headset object
            self.headset = Headset()
            #Set the message callback to ExampleMessageCallback
            self.headset.SetMessageCallback(ExampleMessageCallback)
            #Retrieves the command line arguments
            args = getattr(sys, 'argv', [''])
            #Set the default port to the first command line argument based on the parameter provided by user
            default_port = self.params['COM Port']
            #Connect the headset
            self.headset.Connect(default_port)
            #Start the data acquisition based on the parameter provided by user
            if arg.lower().startswith('imp'):
                #Currently not used
                self.headset.SetSampleCallback(ExampleSampleCallback_Impedances, 0)
                self.headset.StartImpedanceDriver()
            else:
                #Set the sample callback to ExampleSampleCallback_Signals
                self.headset.SetSampleCallback(ExampleSampleCallback_Signals, 0)
                if len(arg.strip()): self.headset.SetDefaultReference(arg, True)
            #Start the data acquisition
            self.headset.StartBackgroundAcquisition()


        def loop(self):
            #Called every loop based on the user's chosen frequency
            global data_buffer
            #If the data buffer has data, then set the output to the data buffer
            if len(data_buffer.keys()) > 0:
                self.set_output(stream_name = 'DSI-24', data = data_buffer.get_data('DSI-24'), timestamp = data_buffer.get_timestamps('DSI-24'))
                #Clear the data buffer
                data_buffer.clear_stream_buffer_data('DSI-24')

        def cleanup(self):
            #Called when the script is stopped
            global data_buffer
            global is_first_time
            global time_offset
            #Stop the data acquisition
            self.headset.StopBackgroundAcquisition()
            #Disconnect the headset
            time_offset = 0
            is_first_time = True
            self.headset.Disconnect()
            data_buffer.clear_buffer()

4. *Add* an output and name it "DSI-24".

5. Change the number of channels to 24.

6. *Add* a **Parameter Widget** to the script by clicking on the + sign.

7. Change the name of the parameter to *COM Port* and switch the type of input to "str" so that we can input the COM port.

8. Input the name of the COM port that you noted earlier. This will allow for PhysioLabXR to connect to the DSI-24.

9. Press *Run*.

.. figure:: media/WearableSensing_Scripting.gif
   :width: 800
   :align: center
   :alt: Running in PhysioLabXR

Now the data stream is available as an LSL stream named DSI-24.

10. To view the data stream, go to the **Stream Tab**.

11. Input *DSI-24* in the Add Stream box and press the *Add Widget* button.

12. Click the *Start Button* to start data stream.

It is recommended that you change some of the settings for your stream. You can follow the steps `here <https://physiolabxrdocs.readthedocs.io/en/latest/Visualization.html#line-chart>`_ to change the settings.
For this tutorial, we added a Butterworth High Pass Filter with a cutoff frequency of 0.01 Hz, Nominal Sampling Rate of 250 and Group Channels Constant Offset to 50 by first pressing on the Gear icon and then on the top Default Group name.

.. figure:: media/WearableSensing_Stream.gif
   :width: 800
   :align: center
   :alt: Viewing the stream on the Stream Tab

13. To see the FFT of the signals from the `DSI-24 <https://wearablesensing.com/dsi-24/>`_ right-click on the stream and select *FFT*.

.. figure:: media/WearableSensing_FFT.gif
   :width: 800
   :align: center
   :alt: Showing FFT of data stream

**Note** A more updated/indepth version of this script can be found in this path:
physiolabxr\interfaces\DeviceInterface\DSI24 which has 3 files to integrate the device to PhysioLabXR.


Now that you have the data streaming: check out these docs:

- :ref: `How to save the data <Recording>`_
- :ref: `How to run a classification model on the data <tutorials/BuildMultiModalERPClassifier>`_
- :ref: `Need help connecting the DSI-24 with Varjo's XR-3? <WearableSensingxVario>`_




