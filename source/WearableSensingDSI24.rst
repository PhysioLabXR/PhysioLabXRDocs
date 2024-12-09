.. _Wearable Sensing Stream:

############################################
Stream data from Wearable Sensing's DSI-24
############################################

This tutorial will demonstrate how to connect the DSI-24 to your computer and PhysioLab\ :sup:`XR`.
The `DSI-24 <https://wearablesensing.com/dsi-24/>`_ is `Wearable Sensing's <https://wearablesensing.com/>`_ dry electrode EEG system.
This tutorial will be primarily focused on the Windows platform.
This tutorial currently only works with the latest stable developmental version of PhysioLabXR. If you would like to know how to download the latest stable version of PhysioLabXR, you can check that out
`Here <https://physiolabxrdocs.readthedocs.io/en/latest/Contribute.html#run-from-source>`_.


.. raw :: html

    <p align="center">
        <iframe width="560" height="315" src="https://www.youtube.com/embed/ExMdOJoQMc4?si=gM_J_3OZhBbGYdTI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
    </p>

If you would like to be more hands on and work with the data from DSI-24 as well as implement other functions from the DSI-24 you can check out `this <Wearable Sensing Stream Old>`_.

1. Ensure you have the batteries installed the DIS-24.

2. Turn on the `DSI-24 <https://wearablesensing.com/dsi-24/>`_ by double clicking on the power button.

3. Go to Device Manager on your computer.

4. Expand the Bluetooth section.

5. Disable Intel(R) Wireless Bluetooth(R) or your default Bluetooth driver (You can reenable at anytime, however if left enabled the DSI-24 will not work correctly).

6. Plug in the Bluetooth adapter provided by `Wearable Sensing's <https://wearablesensing.com/>`_ to any USB port.

7. Go to Bluetooth settings.

8. Scroll down to device settings.

9. Turn the Bluetooth devices discovery setting from Default to Advanced. (If you are on Windows 10, this step can be ignored).

10. Plug in the Bluetooth adapter provided by Wearable Sensing.

11. Go to Bluetooth settings.

12. Scroll down to device settings.

13. Turn the Bluetooth devices discovery setting from Default to Advanced. (If you are on Windows 10, this step can be ignored).z

14. Connect the `DSI-24 <https://wearablesensing.com/dsi-24/>`_ to your device as if you would connect a mouse or headphones. (Should be named DSI24-{*serial number*}).

15. Go back to Bluetooth Settings.

16. Scroll down to more Related Settings.

17. Click on more Bluetooth Settings.

18. Click on COM Ports and make note of the COM port number that has the name: DSI24-{*serial number*} "Bluetooth Serial Port".

19. Connect the DSI-24 to your device as if you would connect a mouse or headphones. (Should be named DSI24-{*serial number*}). It may state that the device isn't connected, this is *normal*.

20. Go back to Bluetooth Settings.

21. Scroll down to more Related Settings.

22. Click on more Bluetooth Settings.

23. Click on COM Ports and make note of the COM port number that has the name: DSI24-{*serial number*} "Bluetooth Serial Port".

24. Now we can open PhysioLabXR.

25. In the Stream input line, you can type out "DSI24". This should open up the DSI-24 interface where the EEG data will be displayed as well an options window
where you can input the COM Port as well as whether or not you would like to test the impedance of the electrodes to make sure they are the correct fit. If the options window
does not show you can click on the far right button to open it.

26. Once you have configured your options all you have to do is press the green play button on the bottom of the main window. By right clicking on the graph in the main window you can decide on how you want the data to be displayed.
There are also filters you can choose to add to the data by clicking on the blue button that is the second button from the bottom.

**Note** Occasionally it may say that it cannot connect to the port, you should retry to connect by stopping the program with the red button on the bottom and rerunning it. If that does not work,
we recommend you reconnect the device by disconnecting it in the settings and reconnecting.

Now that you have the data streaming: check out these docs:

- :ref: `How to save the data <Recording>`_
- :ref: `How to run a classification model on the data <tutorials/BuildMultiModalERPClassifier>`_
- :ref: `Need help connecting the DSI-24 with Varjo's XR-3? <WearableSensingxVario>`_
