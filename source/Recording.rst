*****
Recording Data
*****
RenaLabApp's recording API lets you record multi-stream data with synchronized timestamps.

Record data
########
After you added data source in the
In the recording options, you may change where to save the file, and what file format to save the data in.

Working with recorded data
########
Recorded data can be loaded back for further analysis.

Advanced
########

.dats serialization and eviction itnerval
*************
The recording is enabled by serialization interface (RNStream) optimized for data with timestamps from multiple
sources (i.e., EEG, eyetracking and video recorded simultaneously)

Recording files created by RealityNavigation uses .dats file format. (placeholder)

.. image:: media/RN_Stream.png
    :width: 1080

.DATS (dictionary of array and timestamps) is a binary file format used by RealityNavitaion to log the recorded data.