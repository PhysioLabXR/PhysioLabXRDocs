.. _API ref Stream_in:

RenaScript API Reference
**************************


.. _API ref Stream_in setting script output:
..................................................................
Setting output for the script
..................................................................

Setting the output is one of the more frequently used step.

You can use one of the two streaming interface - LSL or ZMQ to stream out the data from the script.

PhysioLab\ :sup:`XR` provides an interface to set the output data from the script, it hides the
complexity of the streaming interface and provides a simple interface to set the output data.

Please refer to the docstring of `physiolabxr.utils.data_utils.validate_output` for
more information on the format of the output data.

A Note on timestamping
-----------------------
If timestamp is not given, whether the data is a single timepoint for a number of timepoints, the local_clock
will be used as timestamps for the data.