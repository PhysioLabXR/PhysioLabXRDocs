View DATS data in MATLAB
============================

To access recorded DATS data in MATLAB follow these steps.

- Install MiniConda_ if it not installed in your system.
- Create_ a new conda environment with the command `conda create --name rena`.
- Activate the conda env using the command `conda activate rena`
- Install the required packages using the command `pip install -r requirements.txt`
- Run the script ParseRNStreamToMatlab.py with 2 arguments - dats file path and the output directory `python offline\ParseDataRNStream.py C:/Recordings/05_24_2021_13_31_12-Exp_myexperiment-Sbj_someone-Ssn_0.dats C:/Recordings`
- Import the .mat file into MATLAB

.. _MiniConda:https://docs.conda.io/en/latest/miniconda.html
.. _Create: :https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-with-commands