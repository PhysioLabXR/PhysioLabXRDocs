########################
Using the ZMQ Interface
########################

What to send over ZMQ
************************

PhysioLab\ :sup:`XR` uses `subscriber-publisher sockets <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_
for ZMQ streams, because this pattern is more scalable when the cardinality of data is high and less error-prone in case of
either the publisher or subscriber process crashes.


Unlike LSL, ZMQ is a messaging library for general use. We define a specific message format for PhysioLab\ :sup:`XR` to use.
When you are creating a ZMQ data source, make sure that you are sending the data in one of the following forms:

#. A frame containing two parts: the topic name, and the data. The topic name must be a string, and must match the stream name
   you set when adding the stream. It is necessary for ZMQ to route the message for `subscriber-publisher sockets <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_
   you have multiple `ZMQ publisher socket <https://learning-0mq-with-pyzmq.readthedocs.io/en/latest/pyzmq/patterns/pubsub.html>`_
   connected to the same socket but publishing to different topics.

#. A frame containing three parts: the topic name, a timestamp, and the data.
   The timestamp must be 8-byte (64-bit) double or 4-byte (32-bit) float, indicating when the data was acquired. You
   can set the timestamp to any value that suits your application. Refer to :ref:`this page <time in physiolab>` for more information on time.

For an detailed example on how to create a ZMQ data source, see :ref:`write your ZMQ data source <zmq data source>`.


.. _ZMQInterface port numbers:
Port numbers
************************

When choosing ports for ZMQ sockets, it is important to note that some ports are reserved for system use, and should not be used. For example, ports 0-1023 are typically reserved for system use.
Also, some ports are reserved for specific applications. For example, port 80 is reserved for HTTP.

Reversed ports
------------------

some ports are reserved by PhysioLab\ :sup:`XR`. These ports are listed below:

+------------------+---------------------------------------+
| Port             | Used by                               |
+==================+=======================================+
| 8000-8100        | :ref:`scripting <feature scripting>`  |
+------------------+---------------------------------------+
| 9980-9990        | :ref:`replay <feature replay>`        |
+------------------+---------------------------------------+

Default ports
------------------

When creating ZMQ streams from :ref:`Replay <replay stream interface>` and Scripting, their port numbers are chosen
from this list. You can always change these ports in the :ref:`user interface <replay stream interface port line edit>`.

+----------------------+-----------------------------------------------+
| Starting port number | Used by                                       |
+======================+===============================================+
| 11000                | :ref:`scripting outputs <feature scripting>`  |
+----------------------+-----------------------------------------------+
| 10000                | :ref:`replay streams <feature replay>`        |
+----------------------+-----------------------------------------------+


.. _zmq data types:

ZMQ Data types
************************

The ZMQ interface supports the following data types:

``uint8, uint16, uint32, uint64, int8, int16, int32, int64, float16, float32, float64``