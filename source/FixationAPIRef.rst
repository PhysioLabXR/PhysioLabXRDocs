.. _fixation:

"""""""""""""""""
Fixation
"""""""""""""""""

..................................................................
physiolabxr.scripting.physio.eyetracking
..................................................................


.. py:function:: gap_fill(gaze_xyz, gaze_status, valid_status, gaze_timestamps, max_gap_time=0.075, verbose=True)

    Fill gap in gaze data with specific threshold.

    :param numpy.ndarray gaze_xyz: ndarray of shape (3, timesteps): x, y, z are the 3D gaze vector given by your eyetracker, with z pointing out of the eye straight ahead

    :param numpy.ndarray gaze_status: ndarray of shape (timesteps, ): the validity of the gaze sample

    :param any valid_status: the value of the valid status, the valid value should be in the gaze_status array

    :param numpy.ndarray gaze_timestamps: the timestamps of the gaze samples (in seconds)

    :param float max_gap_time: maximum gap time, preset to 0.075 seconds

    :param bool verbose: If ``True``, prints information about the interpolation process. Default is ``True``

    :return: gaze_xyz
    :rtype: ``numpy.ndarray``



.. py:function:: fixation_detection_idt(gaze_xyz, timestamps, window_size=0.175, dispersion_threshold_degree=0.5, saccade_min_sample=2, return_last_window_start=False)

    Fixation detection using the IDT algorithm.

    :param numpy.ndarray gaze_xyz: 2D numpy array containing (x, y, z) coordinates of gaze points

    :param numpy.ndarray timestamps: timestamps corresponding to gaze data points

    :param float window_size: Size of the time window for fixation detection (in seconds). Default is 0.175 seconds.

    :param float dispersion_threshold_degree: Threshold for dispersion angle to determine fixations (in degrees). Default is 0.5 degrees.

    :param int saccade_min_sample:  the minimal number of samples between consecutive fixations to be considered as a saccade, preset to 2.

    :param bool return_last_window_start: If ``True``, returns the start of the last window. Default is ``False``.

    :return: A 2D numpy array containing fixation labels (1 for fixation, 0 for non-fixation) and corresponding center timestamps.
    :rtype: ``numpy.ndarray``

