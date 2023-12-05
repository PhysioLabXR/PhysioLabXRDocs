.. _RNStream:

"""""""""""""""""
RNStream
"""""""""""""""""

..................................................................
physiolabxr.utils.RNStream
..................................................................



.. py:class:: physiolabxr.utils.RNStream

RNStream is a class that provides a simple interface for serialization and deserialization of multimodal data streams.
You can read more about its usage in the :ref:`Recording <feature recording>` section.

.. py:function:: __init__(file_path)

        Create a RNStream object, associate it with a file path.

        :param str file_path: the file path to which the stream will be written to or read from.
                              When :ref:`stream_out <RNStreams.stream_out>` is called.
                              If the file exists, calling :ref:`stream_out <RNStreams.stream_out>` will append data to the
                              end of the file. If the file does not exist, it will be created.


.. _RNStreams.stream_out:

.. py:method:: stream_out(buffer)

        serialize the content of the buffer to the file path pointed by self.fn

        :param dict buffer: a dictionary, key is a string for stream name, value is a iterable of two ndarray the first of the two ndarray is the data samples, the second of the two ndarray are the timestamps of the data samples. The time axis for the data array must be the last. The timestamp array must have exactly one dimension (the time dimension). The data and timestamps rray must have the same length in their time dimensions. The timestamps array must also in a increasing order, otherwise a warning will be raised
        :return: the total number of bytes that has been streamed out
        :rtype: int

.. py:method:: stream_in(ignore_stream=None, only_stream=None, jitter_removal=True, reshape_stream_dict=None)

        Streams in data.

        :param list ignore_stream: List of streams that should be ignored.
        :param list only_stream: List of streams that will only be streamed in.
        :param bool jitter_removal: If true, the jitter will be removed from the data.
        :param list reshape_stream_dict: reshape img, time series, time frames data
        :return: a dictionary, key is a string for stream name, value is a iterable of two ndarray: the first of the two ndarray is the data samples, the second of the two ndarray are the timestamps of the data samples.
        :rtype: ``dict``


.. py:method:: stream_in_stepwise(file, buffer, read_bytes_count, ignore_stream=None, only_stream=None, jitter_removal=True, reshape_stream_dict=None)

            Streams in data from a file stepwise, allowing for incremental reading and processing.

            :param str file: The file name to read from.
            :param dict buffer: a dictionary, key is a string for stream name, value is a iterable of two ndarray: the first of the two ndarray is the data samples, the second of the two ndarray are the timestamps of the data samples.
            :param float read_bytes_count: The number of bytes to read from the file.
            :param list ignore_stream: List of streams that should be ignored.
            :param list only_stream: List of streams that will only be streamed in.
            :param bool jitter_removal: If true, the jitter will be removed from the data.
            :param list reshape_stream_dict: reshape img, time series, time frames data
            :return:
                Five elements tuple that consists:

                - **file** : ``str``: The updated file;
                - **buffer** : ``dict``: a dictionary, key is a string for stream name, value is a iterable of two ndarray: the first of the two ndarray is the data samples, the second of the two ndarray are the timestamps of the data samples;
                - **read_bytes_count** : ``float``: Total number of bytes in the file;
                - **total_bytes** : ``int`` : the total number of bytes in the file;
                - **finished**: ``bool`` : True if the file has been read completely, False otherwise
            :rtype: ``tuple``


..................................................................
Example using RNStream
..................................................................

.. seealso::
    :ref:`ScriptingAPIReference`


