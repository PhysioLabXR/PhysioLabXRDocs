.. _get_stream_info:

"""""""""""""""""
get_stream_info API Reference
"""""""""""""""""

..................................................................
physiolabxr.scripting.RenaScript.get_stream_info
..................................................................


.. py:function:: RenaScript.get_stream_info(stream_name,info)

    Get information of a specific stream.

    :param str stream_name: name of the stream
    :param str info: type of information to get.
        Can be one of the following:

        - NominalSamplingRate
        - ChannelNames
        - DataType
    :return: the information requested
    :rtype: ``any``




.. seealso::
    :ref:`RNStream`
