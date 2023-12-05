.. _create stream:

########################################
Stream Your Data in PhysioLab\ :sup:`XR`
########################################

If you have a data source, you can start streaming in PhysioLab\ :sup:`XR` by following the steps below:

.. _create lsl stream:

Stream LSL Data
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Open PhysioLab\ :sup:`XR` (download the App :ref:`here <download>` if you haven't already).

2. Type in the name of the stream you created in the script (e.g. ``python_lsl_my_stream_name`` or ``unity_lsl_my_stream_name`` in :ref:`the LSL examples <stream over LSL>` ) in ``Add Stream Widget``.

3. Select ``LSL`` as the stream type in the dropdown just right where you put in the stream name.

4. Click on the ``Add`` button or hit enter, a stream widget will be added to the main window. The widget will have |stream_available| icon on the bottom. This means that the stream is available on the network. But we are not receiving and plotting any data yet.

5. Click on the |ico6| button to start streaming the data.

6. You will be prompted to auto-set the number of channels to what's being streamed from your script above. This is because the default number of channels is 1 for newly added stream widget. Click on ``Yes`` to auto-set the number of channels.

7. The |stream_available| icon will change to |stream_active| icon. You will see the data being plotted in line chart (:ref:`explore other visualization method <feature visualization>`).


.. _create zmq stream:

Stream ZMQ Data
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


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


