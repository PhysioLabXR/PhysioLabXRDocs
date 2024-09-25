.. _stream using native plugin:

############################################################
Native Device Stream Plugin
############################################################

create a device interface

Note on blocking operations (e.g., pull data from device)

Recommend to a separate process for device communication

Test your interface with the device

integrate the device interface with PhysioLab\ :sup:`XR`

relevant files and directories

Files you need to create:
physiolabxr/_presets/DevicePresets/<DeviceName>.json
physiolabxr/interfaces/<DeviceName>/__init__.py
physiolabxr/interfaces/<DeviceName>/<DeviceName>_Interface.py
physiolabxr/interfaces/<DeviceName>/<DeviceName>_Options.py: this is optional. It offers great flexibility to
interact with the device in the ways you want. For example, if you need to set the COM port for a serial device, you can
add a COM port option to the class <DeviceName>Options in this file.
physiolabxr/_ui/<DeviceName>_Options.ui: this is optional. Only when you have <DeviceName>_Options.py

Files you want to read and understand:
physiolabxr/interfaces/DeviceInterface/DeviceInterface.py
physiolabxr/threadings/DeviceWorker.py

Files that are helpful to read if you want to have a more in-depth understanding of the data flow:
physiolabxr/ui/DeviceWidget.py
physiolabxr/ui/BaseStreamWidget.py


for a stream name to be recognized as a device with native plugin, you need to add the device preset json file.
add the device preset json file

device availability

Test cases


Make a pull request to share your plugin with the community.

Checklist before submitting a pull request: