"""""""""""""""""
ERP API Reference
"""""""""""""""""

..................................................................
physiolabxr.scripting.physio.epochs
..................................................................


.. py:function:: get_event_locked_data(event_marker, data, events_of_interest, tmin, tmax, srate, return_last_event_time=False, verbose=None)

    Get event locked data.

    :param tuple event_marker: tuple of event marker and its timestamps

    :param tuple data: tuple of data and its timestamps

    :param list events_of_interest: iterable of event markers with which to get event aligned data

    :param float tmin: minimum time

    :param float tmax: maximum time

    :param float srate: sampling rate

    :param bool return_last_event_time: whether to return the time of the last found in the data

    :param bool verbose: If ``True``, prints information about the event locked data. Default is ``True``

    :return: dictionary of event marker and its corresponding event locked data. The keys are the event markers
    :rtype: ``dict``




.. py:function:: buffer_event_locked_data(event_locked_data, buffer)

    Buffer event locked data.

    :param dict event_locked_data: dictionary of event marker and its corresponding event locked data. The keys are the event markers.

    :param dict buffer: dictionary of event marker and its corresponding buffer. The keys are the event markers.

    :return: dictionary of event marker and its corresponding event locked data. The keys are the event markers.
    :rtype: ``dict``


.. py:function:: get_baselined_event_locked_data(event_locked_data, pick: int, baseline_t, srate)

    get baselined event locked data

    :param dict event_locked_data: dictionary of event marker and its corresponding event locked data. The keys are the event markers.

    :param int pick: index of the channel to baseline.

    :param tuple baseline_t: baseline time

    :param float srate: sampling rate


    :return: dictionary of event marker and its corresponding event locked data. The keys are the event markers.
    :rtype: ``dict``
