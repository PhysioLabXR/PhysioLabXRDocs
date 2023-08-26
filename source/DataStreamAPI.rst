********************
Data Stream API
********************

Streams are basic building blocks of any PhysioLab\ :sup:`XR` paradigm. They are the data sources

Choosing which data stream API to use
--------------------------------------
There are multiple ways to establish connection between a data source and RenaLabApp. User can use either LSL, ZMQ, or
a custom device worker; each method is more involved than the previous depending on the how well acquitted you are with the
device. Generally, the decision for choosing the connection method goes like this:

.. image:: media/ChoosingConnectionMethod.png
    :width: 1080

The following section covers the detailed steps for each method.

Using LSL
-------------------

Write your LSL Interface
--------------------------

Using ZMQ
-------------------

Write your ZMQ Interface
--------------------------

Choosing ZMQ ports
~~~~~~~~~~~~~~~~~~

Write your data worker class
---------------------------------
