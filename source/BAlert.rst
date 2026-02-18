###############################################################################################
PhysioLab\ :sup:`XR` + ABM B-Alert X24
###############################################################################################

This tutorial will demonstrate how to connect the B-Alert X24 EEG headset to your computer and PhysioLab\ :sup:`XR`.
The `B-Alert Mobile <https://www.advancedbrainmonitoring.com/products/b-alert-mobile>`_ by `Advanced Brain Monitoring <https://www.advancedbrainmonitoring.com/>`_ is a wireless, research-grade EEG system with real-time impedance checks and SDK integration. Common configurations provide 20 EEG channels, and samples at 256 Hz.
This tutorial will be primarily focused on the Windows platform.

1. Turn on the BAlert.
2. Launch PhysioLab\ :sup:`XR`.
3. Go to the **Stream Tab**.
4. Enter or select the dropdown menu for the **BAlert**.
5. Click on the **+Add** button.
6. (You can start streaming after the steps below.) Click on the **Start** button to begin streaming data from the BAlert to PhysioLab\ :sup:`XR`.

.. note:: B-Alert X24 streams 24 channels: 20 EEG, 1 EKG, and AUX1/2/3. Impedance check applies to the 20 EEG leads only.

.. raw:: html

   <iframe width="560" height="315"
           src="https://www.youtube.com/embed/IHVdXZzosoQ"
           frameborder="0" allowfullscreen></iframe>

License (Options window)
7.	Open the Options window for B-Alert.
8.	In **Select your ABM license folder**, browse to your license folder.
9.	Click **Sync License**.

.. note:: The app copies needed files into <BAlert.exe>\\License and remembers your selection.

.. raw:: html

   <iframe width="560" height="315"
           src="https://www.youtube.com/embed/1L-0wEBjwQg"
           frameborder="0" allowfullscreen></iframe>

Impedance (Options window)
10.	Click Check Impedance to measure electrode impedances.

Color legend
•	Green (< 40 kΩ): Good
•	Yellow (≥ 40 kΩ and < 80 kΩ): Moderate — improve contact / add gel
•	Red (≥ 80 kΩ): Bad — fix contact
•	Gray (NA): Out of range / no contact

.. note:: Proceed only when all EEG channels are green.

.. raw:: html

   <iframe width="560" height="315"
           src="https://www.youtube.com/embed/oxk9LxykSMQ"
           frameborder="0" allowfullscreen></iframe>
