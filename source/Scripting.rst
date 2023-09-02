
###################################
Scripting
###################################

The scripting interface, or RenaScript, is one of the most extensible features PhysioLabXR offers.
It empowers researchers to run user-defined Python scripts (Python is the only language supported right now), providing full agency for creating and deploying custom data processing pipelines.
With Python's versatility and vast array of fast-growing open-source libraries, users are encouraged to
creatively explore and experiment with novel applications such as close-loop neurofeedback.
Users can implement DSP algorithms, run ML models with real-time data streams,
or communicate with external applications such as a cloud-computing platform through Python APIs.

Learn From a Simple Example How to Create and Run a Processing Script
--------

.. automodule:: module_a
   :members:
   :undoc-members:
   :show-inheritance:

In this example, we will write a script to process our ``Dummy-8Chan`` stream: a randomly generated stream.

First, make sure you have the following packages installed in your Python environment:

- pylsl
- numpy

You can install them with ``pip install pylsl numpy``, or if you use a conda environment, you can install them with
``conda install -c conda-forge pylsl numpy``.

1. We will create a dummy stream to record. Create a new python file, put in the following snippet.

.. literalinclude:: LSLExampleOutlet.py
    :language: python
    :linenos:
    :lines: 1-100

Now run the script in your terminal with a command like ``python LSLExampleOutlet.py``. The script will start an LSL
stream with stream name 'Dummy-8Chan' and stream type 'EEG'. The stream will generate random data with 8 channels and
a sampling rate of 250 Hz. The stream will keep running until you stop the script.

2. Now we will create a new python script to process out Dummy stream. Go to ``Scripting`` tab and click on the ``Add`` button.
A script widget containing some informations of the script should show up. It should look like this:

.. image:: media/scriptingtab.png
    :width: 1080

Let's take a closer look at what each component does:

- *Script Name*: The path of your script file on the system. You don't have to manually type in the path. To create a new script, click on the ``Create`` button beside and specify the path and the name of the script file. Then click save. Alternatively you can load existing scripts by clicking the ``Locate`` button.

- *Run Frequency (time per second)*: The maximum frequency the script will run at. The real running frequency won't necessarily be exactly the same, it can be limited by the performance of your device.

- *Input Buffer Duration (sec)*: The size of the buffer for receiving input data from network interfaces, in seconds. The buffer is implemented as a FIFO (First In First Out) queue and this parameter will determine the maximum data you can access to in during a loop of your script.

- *Inputs Pane*: Where you can add streams to your script as input. To add a stream as an input, you should first make sure it's already added in the ``stream`` tab. Added input streams can be accessed in the script using ``self.inputs[my_stream_name]``. Accessing the input gives a data matrix and the timestamp vector. The data matrix is of shape (f :sub:`nominal` ∗ T :sub:`bufferduration`, N :sub:`channels`), where f :sub:`nominal` is the nominal sampling rate, T :sub:`bufferduration` is the amount of data in seconds the buffer contains and N :sub:`channels` is the number of channels for this stream. The timestamp vector has f :sub:`nominal` ∗ T :sub:`bufferduration` items, each corresponding to a row of entries in the data matrix

- *Output Pane*: Where you can create output streams. An output is defined by the output stream name and its number of channels. The output stream will be broadcasted on the network through LSL or ZMQ per the user’s choice, allowing it to be visualized the same way as an external data source in the stream tab. Meanwhile, a script may take output streams from another script as input, thus creating a cascading pipeline. You can assign the data to transmit in your script by assigning ``self.outputs[my_stream_name]`` in your script.

- *Parameters Pane*: Where you can initiate variables that the user wishes to alter during runtime or variables that depend on the host computer (e.g., the path to a pre-trained ML model) when the researcher wants to avoid hardcoding these variables into the script. You can define parameters by their names, data type, and values. Then throughout the script, parameters are accessible using its name: ``self.params[my_parameter_name]``. Changes to parameter values will be reflected immediately in real time while the scripting is running.

- *Console Log*: The button to bring put console log

- *Run/Stop/Kill*: The button to run the script, it will change into stop when the script starts to run. When stop is clicked, the button will changed to kill, so you can kill the process manually if it's not closed automatically.

- *Simulate Input check box*: Check the box if you want to use randomly generated data for all your input streams.

- *Loop (with overheads) per second*: This is where you can monitor the performance of the script. The number shows how many seconds the script needed to run for one loop.

- *Average loop call runtime*: The average runtime of a loop.

Module B
--------

.. automodule:: module_b
   :members:
   :undoc-members:
   :show-inheritance:


Inputs
******


Outputs
*******


Install Packages in the Scripting Environment
**********************************************