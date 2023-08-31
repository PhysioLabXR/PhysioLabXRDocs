###################################
Scripting
###################################

The scripting interface, or RenaScript, is one of the most extensible features PhysioLabXR offers.
It empowers researchers to run user-defined Python scripts, providing full agency for creating and deploying custom data processing pipelines.
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

We will create a dummy stream to record. Create a new python file, put in the following snippet.

.. literalinclude:: LSLExampleOutlet.py
    :language: python
    :linenos:
    :lines: 1-100

Now run the script in your terminal with a command like ``python LSLExampleOutlet.py``. The script will start an LSL
stream with stream name 'Dummy-8Chan' and stream type 'EEG'. The stream will generate random data with 8 channels and
a sampling rate of 250 Hz. The stream will keep running until you stop the script.

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