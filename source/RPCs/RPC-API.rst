
.. _RPC-API:

##########################################################
Use Remote Procedure Call (RPC) in PhysioLabXR
##########################################################


Requirements for RPC functions
==================================
Functions


.. _RPC-compiler:

RPC Compiler
================

The RPC compiler expects the following for functions decorated with `@rpc`:

- The function must be a member of the `PhyScript` (formly known as `RenaScript`) class.
- The function must have `type hints <https://docs.python.org/3/library/typing.html>`_ for its arguments. The type hints must be one of the supported types (see below).
- The function may or may not have return `type hints <https://docs.python.org/3/library/typing.html>`_, but if it does, it must be one of the supported types (see below).

The `type hints <https://docs.python.org/3/library/typing.html>`_ are necessary for the RPC compiler to generate the
correct protocol buffers for request and response used for the RPC function.


Supported Types
----------------


Currently, the RPC compiler supports the following types as its arguments and return types:

+----------------+-------------+
| Proto Buf Type | Python Type |
+================+=============+
| int32          | int         |
+----------------+-------------+
| float          | float       |
+----------------+-------------+
| str            | string      |
+----------------+-------------+
| bool           | bool        |
+----------------+-------------+

If any type hints for the arguments or returns is the unsupported type, the RPC compiler will raise an error.



Return Type
----------------


Argument and Return Type
=========================

To call the RPC functions, you need to pass in its *request*. The return from calling an RPC function is a *response*.

The PhysioLab RPC compiler will generate the reqeust

