.. _scripting api reference:

#######################
Scripting API Reference
#######################


Eyetracking
===========

.. _scripting api gap fill:


.. function:: gap_fill(gaze_xyz, gaze_status, valid_status, gaze_timestamps, max_gap_time=0.075, verbose=True)

   Fills gaps in eye-tracking data with interpolation and handles ignored gaps.

   :param gaze_xyz: A NumPy array containing 3D gaze coordinates (x, y, z).
   :type gaze_xyz: :class:`numpy.ndarray`

   :param gaze_status: An array indicating the status of gaze data (e.g., tracked or lost).
   :type gaze_status: :class:`numpy.ndarray`

   :param valid_status: An array indicating the validity status of gaze data.
   :type valid_status: :class:`numpy.ndarray`

   :param gaze_timestamps: An array of timestamps corresponding to gaze data.
   :type gaze_timestamps: :class:`numpy.ndarray`

   :param max_gap_time: The maximum allowable gap duration for interpolation in seconds (default: 0.075 seconds).
   :type max_gap_time: float

   :param verbose: Whether to print verbose information about gap filling (default: True).
   :type verbose: bool

   :return: A NumPy array with gaps in the eye-tracking data filled by interpolation.
   :rtype: :class:`numpy.ndarray`

   This function takes eye-tracking data, including gaze coordinates, gaze status, validity status, and timestamps, and performs gap filling. Gaps in the data are identified and categorized as interpolated gaps or ignored gaps based on the specified `max_gap_time`. Interpolated gaps are filled with interpolated values, while ignored gaps are marked as NaN.

   The function returns the gap-filled eye-tracking data as a NumPy array.

   Example usage:

   .. code-block:: python

      import numpy as np
      from your_module import gap_fill

      # Define your eye-tracking data
      gaze_xyz = np.array(...)
      gaze_status = np.array(...)
      valid_status = np.array(...)
      gaze_timestamps = np.array(...)

      # Fill gaps in the data
      filled_data = gap_fill(gaze_xyz, gaze_status, valid_status, gaze_timestamps)
