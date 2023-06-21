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

.. note::
    Loading .dats and .csv files can be slow

Working with recorded data
########
Recorded data can be loaded back for further analysis.

.dats
    For loading DATs files you can use the steam_in function in ReNaLabApp package

.pickle
    For loading pickle files you can use the pickle package in Python

.m
    For loading matlab files you can use scipy's loadmat function

.csv/.xdf
    For loading csv and xdf files we recommend using the load_csv/load_xdf function in in ReNaLabApp package since the screen recording data has multiple dimensions and is stored differently in the csv files. The load_csv function can automatically handle the data shape transformation.

To use load functions in ReNaLabApp package, you can pip install ReNaLabApp and import ReNaLabApp.


Advanced
########

.dats serialization and eviction interval
*************
The recording is enabled by serialization interface (RNStream) optimized for data with timestamps from multiple
sources (i.e., EEG, eyetracking and video recorded simultaneously)

Recording files created by RealityNavigation uses .dats file format. .DATS (dictionary of array and timestamps) is a binary file format used by RealityNavitaion to log the recorded data.
The structure of .DATS is shown in the figure below

.. image:: media/RN_Stream.png
    :width: 1080

The .DATS data consists of multiple chunks of data samples, each chunk containing different types of stream packets. ReNaLabApp supports various types of data stream packets, including EEG
packets, event marker packets, and screen capture packets. All these streams of data is recorded in a fixed time window called Eviction Interval. Once the data has been recorded for this
specified duration, the information stored in the memory buffer will be transferred to the disk.

Within data packets, each packet is composed of seven domains. Firstly, a magic code is used to distinguish different data packets, followed by a 32-byte domain of stream name.
(In that sense, the stream name is constrained to be less than 32 charactors). Next, we have a 1 byte domain representing
data type and 1 byte representing number of dimensions. Subsequently, the actual dimensions of the data array are provided, and its length is determined by the number of dimensions.
Finally, the data array itself, along with the accompanying time stamp array, is stored.

Developer
#########

:ref:`Go to <../developer.rst>`