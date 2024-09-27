.. _feature rpc-unity-async:

#############################################################
Async RPC with C# (Unity) for long-running tasks
#############################################################

In some cases, you may have a long-running function defined in PhysioLab\ :sup:`XR` script. It may be a training a machine learning model,
running a simulation, or any other task that takes a long time to complete.
In such cases, you may not want to block the Unity application while waiting for the task to complete.

When to use Async RPC and why
==============================

.. note::

    We recommend you go through the :ref:`basic Unity-RPC tutorial <feature rpc-unity>` before continuing.


Recall in basic :ref:`tutorial for using RPC in Unity<feature rpc-unity>`,
we used the following code to call an RPC function from Unity:

.. code-block:: csharp

   public void OnCallRPC()
    {
        Debug.Log("Calling RPC");
        var reply = client.ExampleOneArgOneReturn(new ExampleOneArgOneReturnRequest { Input0 = "Unity" });
        Debug.Log(reply.ToString());

        // ...
    }

This function is called when you click a button in the example Unity game. If the RPC function
takes a long time to complete, the Unity application will be blocked until this line :code:`var reply = client.ExampleOneArgOneReturn(new ExampleOneArgOneReturnRequest { Input0 = "Unity" });`
returns.

Furthermore, you can imagine if you need to call some RPC in every Unity frame update, the application will be become laggy
if your RPC takes longer than a frame to complete, which is usually 16ms for 60fps, a very hard constraint to meet for most
non-trivial tasks.

This is where **Asynchronous RPC** comes in. By using async RPC, you can call the RPC function and continue with other tasks
without waiting for the RPC function to complete. When the RPC function completes, it will call a callback function that you
can define in your Unity script.

How to use Async RPC
====================

In this section, we will show you how to use Async RPC in Unity with a simple example.

Here's a video showing the steps you need to take to use Async RPC in Unity.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="../_static/async-rpc-unity.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

Here's the step-by-step guide to using Async RPC in Unity, with more contextual information.

1. Setting things up
---------------------

First, if you haven't already, you need to install the GROC dependency in Unity.

In the Unity Editor, open the Package Manager, click on the `+s` button and select `Add package from git URL <https://docs.unity3d.com/Manual/upm-git.html>`_.
Add the following URL to the package manager:

.. code-block:: bash

    https://github.com/PhysioLabXR/Unity-PhysioLabXR-Package.git

PhysioLabXR's Unity Package has more than just the grpc package. Read more about it :ref:`here <LSLZMQUnityPackage>`.

Alternatively, you can install the grpc package manually by following the instructions :ref:`here <rpc-unity-manual-grpc-installation>`.

2. Define the RPC function in PhyScript.
----------------------------------------

Similar to :ref:`the regular RPC <feature rpc basic concept>`, to make a function you defined in PhyScript an async RPC.
Just add the `@async_rpc` decorator to the function. And just like a regular RPC, the function must have type hints
for all its arguments and return value, if there is any.

.. note::

    If you are not familiar with scripting in PhysioLab\ :sup:`XR`, you can learn more about it :ref:`here <feature scripting>`.


In the following example script, we define two async RPC functions:

* ``AsyncOneArgOneReturn`` that takes a string as an argument and returns a string. 
* ``AsyncRPCOneArgNoReturn`` that takes a string as an argument and returns nothing. 
* ``AsyncRPCNoArgNoReturn`` that takes no arguments and returns nothing. 

There are times when you may have a function (rpc) that does not have a *return value*. This is helpful when you want to initiate a long-running async function (e.g., train a ML model), but do not have anything to return after it is done.


.. code-block:: python

   from physiolabxr.rpc.decorator import rpc, async_rpc
    from physiolabxr.scripting.RenaScript import RenaScript
    from google.protobuf.empty_pb2 import Empty
    class AsyncRPCExample(RenaScript):
        def __init__(self, *args, **kwargs):
            """Please do not edit this function"""
            super().__init__(*args, **kwargs)

        # Start will be called once when the run button is hit.
        def init(self):
            pass

        # loop is called <Run Frequency> times per second
        def loop(self):
            print(f'Loop: async server')

        # cleanup is called when the stop button is hit
        def cleanup(self):
            print('Cleanup function is called')

        @async_rpc
        def AsyncOneArgOneReturn(self, input0: str) -> str:
            """
            This is an example of a RPC method that takes one argument and returns one value.

            It is an async rpc method. The input protobuf is called AsyncOneArgOneReturnRequest,

            The input protobuf is called ExampleOneArgOneReturnRequest,
            and the output is called ExampleOneArgOneReturnResponse
            Args:
                input0 (str): the input
            """
            self.LongRunningTask()
            return f"received: {input0}"

        @async_rpc
        def AsyncRPCOneArgNoReturn(self, input0: str) -> None:
            """
            This is an async RPC method that takes one argument but returns no value
            Args:
                input0 (str): the input string
            """
            self.LongRunningTask()
            print(f"Received input: {input0}")
            return Empty()

        @async_rpc
        def AsyncRPCNoArgNoReturn(self, empty:Empty) -> Empty:
            """
            This is an async RPC method that takes no arguments and returns nothing.
            Args:
                empty (Empty): no input expected just passing Empty.
            """
            self.LongRunningTask()
            print("Server received the no-input call")
            return Empty()

        def LongRunningTask(self):
            cycle = 100000
            for i in range(cycle):
                print(f"Spinning {i}/{cycle}")


In all these functions, we run a ``LongRunningTask``, where we have a for loop that runs 100,000 times to simulate a long-running task.
Add this script to the `Scripting tab` in PhysioLab\ :sup:`XR`.

3. Create a Unity client.
----------------------------

We will import sample Unity async scene and scripts from the PhysioLab\ :sup:`XR` package.


Start from a Unity project, import the PhysioLab\ :sup:`XR` package, go to Window->Package Manager and click the plus button
at the top-left corner. Select 'Add package from git URL' from the dropdown.
Add this link https://github.com/PhysioLabXR/Unity-PhysioLabXR-Package.git. For more details, refer to the :ref:`Unity Package docs <LSLZMQUnityPackage>`.
Once the package is imported, you can find the samples in the package manager by selecting the PhysioLabXR package and clicking on the `Samples` tab.
Import the RPC samples from the package.

The async sample scene, once imported, is located at `Samples/GRPC/AsyncRPC/AsyncRPCExample.unity`.

The client script that calls the RPC is located at `Samples/GRPC/AsyncRPC/AsyncExampleClient.cs`. The client is fairly
simple. It has a button that calls the async RPC function when clicked. The response from the RPC function is displayed
in a text object on the screen. There is a rotating cube in the background to show that the Unity application is not blocked
while the RPC function is running.

Here's the complete client code for your reference:

.. code-block:: csharp

    using System;
    using System.Collections;
    using System.Diagnostics;
    using System.Runtime.InteropServices;
    using Cysharp.Net.Http;
    using Grpc.Core;
    using Grpc.Net.Client;
    using TMPro;
    using UnityEngine;
    using UnityEngine.UI;
    using Google.Protobuf.WellKnownTypes;

    public class AsyncExampleClient : MonoBehaviour
    {
        public Button Button;
        public TMP_Text Text;
        public TMP_InputField userInputField;

        private GrpcChannel channel;
        private YetAnotherHttpHandler handler;
        private AsyncRPCExample.AsyncRPCExampleClient client;

        public string host = "http://localhost:13004";

        // Start is called before the first frame update
        void Start()
        {
            Button.onClick.AddListener(OnCallRPC);

            // Set up the RPC client
            handler = new YetAnotherHttpHandler(){Http2Only = true};  // GRPC requires HTTP/2
            channel = GrpcChannel.ForAddress(host, new GrpcChannelOptions() { HttpHandler = handler, Credentials = ChannelCredentials.Insecure});
            client = new AsyncRPCExample.AsyncRPCExampleClient(channel);
        }

        // Update is called once per frame
        void Update()
        {

        }


        public void OnCallRPC()
        {
            Debug.Log("Calling RPC");

            StartCoroutine(CallLongAsyncRPC("World"));

            Debug.Log("Sent RPC request, started waiting for response");
        }

        private IEnumerator CallLongAsyncRPC(string message)
        {
            var request = new AsyncOneArgOneReturnRequest() { Input0 = userInputField.text ?? " " };  // The argument cannot be an empty string
            var call = client.AsyncOneArgOneREturnAsync(request);  // The method name in the client is "<method name in RenaScript>Async"
            yield return new WaitUntil(() => call.ResponseAsync.IsCompleted);

            if (call.ResponseAsync.IsCompletedSuccessfully)
            {
                var response = call.ResponseAsync.Result;
                Text.text = $"[{DateTime.Now}] {response}";
            }
            else
            {
                Debug.LogError("gRPC call failed: " + call.ResponseAsync.Exception);
            }
        }

        // The following is the code for if your RPC function has an input value but no output value
        public void OnCallRPCNoReturn(string variable)
        {
            Debug.Log($"Calling RPC with input: {variable}")
            StartCoroutine(CallLongRPCNoReturnWithInput(variable))
        }
        private IEnumerator CallLongAsyncRPCNoReturnwithInput(string input)
        {
            var request = new InputMesssage() { input0 = input{ } };=
            var call = client.AsyncRPCOneArgNoReturnAsync(request);
            yield return new DuplicateWaitObjectExceptionUntil(() => call.ResponseAsync.IsCompleted);

            if (call.ResponseAsync.IsCompletedSuccessfully)
            {
                Debug.Log("RPC call with no return completed successfully.")
            }
            else
            {
                Debug.LogError($"RPC call failed": { call.ResponseAsync.Exception}"")
            }

        }


        // The following is the code for if your RPC function has no value input and no value output.
        public void OnCallRPCNoReturnNoInput()
        {
            Debug.Log("Calling RPC with no input")
            StartCoroutine(CallLongAsyncRPCNoReturnNo());
        }
        private IEnumerator CallLongAsyncRPCNoReturn()
        {
            var request = new Empty();
            var call = client.AsyncRPCNoArgNoReturnAync(request);
            yield return new DuplicateWaitObjectExceptionUntil(() => call.ResponseAsync.IsCompleted);

            if (call.ResponseAsync.IsCompletedSuccessfully)
            {
                Debug.Log("RPC call with no return completed successfully.")
            }
            else
            {
                Debug.LogError($"RPC call failed": { call.ResponseAsync.Exception} "")
            }

        }


    }

4. Compile the client code, start the server
---------------------------------------------

You can now generate the C# client code for this async RPC function. To do so, open `RPC Options` in the script widget,
under `Outputs`, click `Add`. Change the output type to `CSHARP`, then click `Locate` to set the output path to in Unity project.
It can be under where as long as it's in the Unity project's asset folder. Now run the script, it will start the server and
generate two files: `AsyncRPCExample.cs` and `AsyncRPCExampleGrpc.cs`.

Once the server is started, it will show the RPC port number of the `RPC Options` button. Note this port number, you will need
it in the Unity client in the next step.

.. note::

    The sample that comes with the PhysioLab\ :sup:`XR` package already has the C# client code generated.
    You can find it in the `Samples/GRPC/AsyncRPC` folder. In the demo video, we demonstrate the file generation
    by deleting the generated files and running the script again.

.. note::

    You only need to set the output path when you

    * add new RPC functions
    * change the name of existing RPC functions
    * change the arguments or return values of existing RPC functions

    because the above changes will change how the client calls RPCs from the server, there the client files
    (C# in this case) need to be regenerated.

5. Run the Unity client
------------------------

Go back to the sample scene `AsyncRPCExample.unity`, in the Hierarchy, select the `AsyncRPCClient` object,
and in the `AsyncExampleClient` script component, make sure the `Host` field reads `http://localhost:<port number from the last step>`.
Then enter Play Mode in Unity.

You can also monitor if an RPC is running by its prints from PhysioLab\ :sup:`XR`'s script console.

Click the `Press Me` button in the game window. You should see the response from the RPC function after a few seconds.
You will notice that the Unity application is not blocked while the RPC function is running by the fact that the cube in the background
is still rotating.
