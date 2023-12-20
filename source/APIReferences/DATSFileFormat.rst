
DATS (Dictionary of Array and Timestamps) File Format
#####################################################

.dats serialization
--------------------
The recording is enabled by a serialization interface (:ref:`RNStream<RNStream>`)
optimized for data with timestamps from multiple
sources (i.e., EEG, eyetracking and video recorded simultaneously)

PhysioLabXR\ :sup:`XR` uses .dats (dictionary of array and timestamps) file format.
It is a binary file format used to serialize the recorded data.
The structure of .dats is shown in the figure below

.. image:: media/RN_Stream.png
    :width: 1080

Eviction Interval
-----------------

The file content is first segmented by eviction intervals. Within each interval, each type-length-
value (TLV) packet contains the data for individual streams. The figure shows the anatomy of a TLV packet. Starting with a delimiter sequence
called magic, the packet contains the data array and timestamps preceded by meta information: stream name, data type, number of dimensions, and
the shape of the data. When loading .dats back, the loader uses the dimension information to determine the number of bytes to read as data and
timestamp

Once recording starts, all the data streams are routed to a specialized buffer.
The buffer’s content is retained in the host’s memory until the eviction interval is hit. Default at one second, the
eviction interval controls how often we offload the buffer to the disk. When the data throughput are high, it is important
to evict the buffer in time to prevent out-of-memory. User may adjust the eviction interval in the settings to optimize
for their use case. During an eviction, each stream’s data and timestamps is appended to the file as a TLV packet

