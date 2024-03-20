
.. _feature rpc:

##########################################################
Remote Procedure Call (RPC)
##########################################################

Remote procedural call (RPC) is a powerful feature in PhysioLab\ :sup:`XR` that allows you to define functions in :ref:`a PhyScript <feature scripting>`,
and you can call these functions from a different environment, such as a different PhyScript, anther Python script, C# in Unity, and more.

A common use case is when your experiment paradigm is designed in Unity and you use :ref:`PhyScript <feature scripting>`
to process the physiological data and provide feedback to the Unity environment. For example, if your experiment needs emotion
classification, you can call a *PhyRPC function* defined in :ref:`PhyScript <feature scripting>` from Unity to perform the classification
and return the result to Unity.

Why RPC?
========

More generally, RPC promotes stateless programming, which is a good practice for parallel programming.
It simplifies data processing pipelines, if you have an close-loop experiment with external stimulus presentation software,
instead of keeping track of the state of the experiment in :ref:`PhyScript <feature scripting>`,
you can simply call the function in the external software to update the state of the experiment, or request specific processing
to be performed on the data such as training a machine learning model, or request an inference from a trained model.

Basic Concept
=============

PhysioLab\ :sup:`XR` builds on top of `gRPC <https://grpc.io/>`_, a mature and language-agnostic RPC framework.
It comes with a compiler that compiles the any functions defined in :ref:`a PhyScript <feature scripting>`
into a remote procedure call (RPC) server. Any functions decorated with the `@rpc` decorator are compiled to RPC functions.

For example, the following function is defined in a PhyScript:

.. code-block:: python

    @rpc
    def SayHello(self, input0: str) -> str:
        return "Hello, %s!" % request.name

When you :ref:`run the script <feature scripting - running the script>`, the compiler will generate all the necessary code to
create an RPC server and expose the `SayHello` function to the RPC client. You can find more information about the compiler
in the :ref:`compiler documentation <RPC-compiler>`.

.. important::

   Functions decorated with `@rpc` has some requirements such as 1) all its argument must have type hints.
   2) must be declared as a method in PhyScript class 3) the first argument must be `self`. Read more about
    the requirements in the :ref:`RPC compiler docs <RPC-compiler>`.

Supported Languages
===================
Currently, PhysioLab\ :sup:`XR`'s native RPC compiler supports Python and C# as the client language.
The client-side code is automatically generated when PhyScript with RPC functions is executed from the :ref:`scripting interface <feature scripting>`.


Get started with a simple example
==================================

Examples are available for server in PhyScript, and clients in Python and C#.

Please find their documentation in the following links:

- :ref:`Python example <rpc-python>`
- :ref:`C# (Unity) example <rpc-unity>`

