########################
Using the ZMQ Interface
########################


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