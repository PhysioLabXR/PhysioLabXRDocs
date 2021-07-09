View DATS data in MATLAB
============================

To access recorded DATS data in MATLAB follow these steps.

First we will need to create a environment for running the format converter script. You could
install the necessary packages in your native environment. But we recommend creating a virtual
environment to help isolate different packages needed for different projects. MiniConda_ is a
good tool for managing virtual environment. We will use it here.

- Install MiniConda_ if it not installed in your system.
- Create_ a new conda environment with the command `conda create --name rena`.
- Activate the conda env using the command `conda activate rena`
- Install the required packages using the command `pip install -r requirements.txt`
- Run the script ParseRNStreamToMatlab.py with 2 arguments - dats file path and the output directory::

    python offline\ParseDataRNStream.py <pathToTheRecordingFileToConvertToMATLABFormat> <pathToTheOutputDirectory>

For example, if the recording file we wish to be convert to MATLAB format is at **C:/Recordings/05_24_2021_13_31_12-Exp_myexperiment-Sbj_someone-Ssn_0.dats**.
And the place we wish the MATLAB data file to be saved is at **C:/Recordings**::

    python offline\ParseDataRNStream.py C:/Recordings/05_24_2021_13_31_12-Exp_myexperiment-Sbj_someone-Ssn_0.dats C:/Recordings

- Import the converted .mat file into MATLAB that was saved to the output directory from the previous step.

.. _MiniConda:https://docs.conda.io/en/latest/miniconda.html
.. _Create: :https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands