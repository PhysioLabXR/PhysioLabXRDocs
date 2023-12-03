.. _buffer_api:

"""""""""""""""""

"""""""""""""""""
Buffer API Class
"""""""""""""""""

..................................................................
physiolabxr.utils.buffers
..................................................................


.. py:class:: physiolabxr.utils.buffers.DataBuffer

    A class to store data from multiple streams.

    :ivar dict buffer: a dictionary to store data from multiple streams, contains 'stream_names', ('frames' , 'timestamps')
    :ivar dict stream_name_buffer_sizes: a dictionary to store buffer size for each stream



.. py:function:: __init__(stream_buffer_sizes)

    Initializes the data buffer.

    :param dict stream_buffer_sizes: a dictionary to specify buffer size

.. py:function:: __getitem__(key)

    get data using a key.

    :param str key: key to get data

.. py:method:: keys()

    get keys of the buffer.

    :return: keys of the buffer
    :rtype: ``list``

.. py:method:: get_stream(stream)

    return buffer from a specific stream.

    :param str stream: stream

    :return: buffer from a specific stream
    :rtype: ``any``


.. py:method:: get_data(stream_name)

    get data from a specific stream name.

    :param str stream_name: name of the stream

    :return: data from a specific stream name
    :rtype: ``numpy.ndarray``

.. py:method:: get_timestamps(self, stream_name)

    get timestamps from a specific stream name.

    :param str stream_name: name of the stream

    :return: timestamps from a specific stream name
    :rtype: ``numpy.ndarray``

.. py:method:: update_buffer(data_dict)

    Updates the buffer with data from a dictionary.


    :param dict data_dict: a dictionary that containing 'stream_name', 'frames' and 'timestamps'


.. py:method:: update_buffers(data_buffer)

    Updates the buffer with data from another buffer.


    :param DataBuffer data_buffer: Another DataBuffer object.



.. py:method:: update_buffer_size(stream_name, size)

    Updates the buffer size from stream.

    :param str stream_name: name of the stream

    :param str size: size of the data buffer


.. py:method:: clear_buffer()

   Clear the entire buffer.

.. py:method:: clear_stream_buffer(stream_name)

    Clear buffer from a specific stream.

    :param str stream_name: name of the stream


.. py:method:: clear_stream_buffer_data(stream_name)

    Remove the buffered data for a stream without removing the existing keys.
        The data and timestamps array will instead become empty arraries

    :param str stream_name: name of the stream


.. py:method:: clear_buffer_data()

   Remove buffered data for all streams without removing the existing keys.
        The data and timestamps array will instead become empty arraries

.. py:method:: clear_stream_up_to(stream_name, timestamp)

    Remove buffered data of a specific stream up to a specific timestamp. The resulting timestamp is guaranteed to be greater than the given cut-to timestamp.

    :param str stream_name: name of the stream

    :param float timestamp: timestamp to clear up to


.. py:method:: clear_stream_up_to_index(stream_name, cut_to_index)

    Clear data of a specific stream up to a specific index.

    :param str stream_name: name of the stream

    :param int cut_to_index: index to clear up to



.. py:method:: clear_up_to(timestamp, ignores=())

    Clear data in all streams up to a specific timestamp, optionally ignoring specified streams. The resulting timestamp is guaranteed to be greater than the given cut-to timestamp.

    :param float timestamp: timestamp to clear up to

    :param list ignores: list of stream names to ignore






