*****
Recording Data
*****
RenaLabApp's recording feature lets you record multi-stream data with synchronized timestamps from various
sources, BioSemi and screen capturing for example. The recorded data can be stored as various data formats which you can choose from the
settings, including '.dats', '.m', '.p', '.csv', '.xdf'.


Record data
########
Steps to record data
    1. Add streams you want to record to the stream tab located in the main window.
    2. After stream widgts show up, click the 'start stream' button.
    3. Now navigate to the recording tab located on the upper left corner in the main window.
    4. (Optional) Click the recording settings button, you can modify the file Path you want to store your data or the data format you want to store.
    5. Click Start recording button, the app will start recording the streams, and you can see the recorded time and file size in real time below.
    6. Click Stop recording button, the app will stop recording and store the recorded data to the path you specified.
You can record the data sources that are added to the stream tab `Stream Page`_.
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