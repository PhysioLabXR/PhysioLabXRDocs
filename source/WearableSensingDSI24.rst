


==================================================

PhysioLab\ :sup:`XR` + Wearable Sensing's DSI-24

==================================================

This tutorial will demonstrate how to connect the DSI-24 to your computer and PhysioLab\ :sup:`XR`.
The DSI-24 is Wearable Sensing's dry electrode EEG system.
This tutorial will be primarily focused on the Windows platform

1. Turn on the DSI-24

2. Go to Device Manager

3. Expand the Bluetooth section

4. Disable Intel(R) Wireless Bluetooth(R) or whatever is your default Bluetooth driver

.. figure:: media/BluetoothDisable.gif
   :width: 800
   :align: center
   :alt: Disabling Bluetooth

5. Plug in the Bluetooth adapter provided by Wearable Sensing

6. Go to Bluetooth settings

7. Scroll down to device settings

8. Turn the Bluetooth devices discovery setting from Default to Advanced. (If you are on Windows 10, this step can be ignored)

.. figure:: media/BluetoothDeviceDiscovery.gif
   :width: 800
   :align: center
   :alt: Turning on Bluetooth Device Discovery

9. Connect the DSI-24 to your device as if you would connect a mouse or headphones. (Should be named DSI24-{*serial number*})

10. Go back to Bluetooth Settings

11. Scroll down to more Related Settings

12. Click on more Bluetooth Settings

13. Click on COM Ports and make note of the COM port number that has the name: DSI24-{*serial number*} "Bluetooth Serial Port"

.. figure:: media/BluetoothCOMPort.gif
   :width: 800
   :align: center
   :alt: Finding the COM Port

14. Open PhysioLab\ :sup:`XR`

15. Go to Scripting

16. Click Locate and find the WearableSensingScript.py

17. Add an output and name it DSI-24

18. Change the number of channels to 24

17. Add a parameter to the script by clicking on the + sign

18. Switch the type of input to "str"

19. Input the name of the COM port that you noted earlier

20. Press run

.. figure:: media/PhysioLabXR_DSI24.gif
   :width: 800
   :align: center
   :alt: Running in PhysioLabXR

(Future updates on impedance checks and other features explained further down)




