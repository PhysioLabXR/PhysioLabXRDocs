.. _LSLZMQUnityPackage:

############################################
Stream Data from Unity via LSL, ZMQ, or GRPC
############################################

We provide a preconfigured `Unity package <https://github.com/PhysioLabXR/Unity-PhysioLabXR-Plugin.git>`_ that wraps LSL, ZMQ and GRPC
dependencies.

Prerequisites
*************

- Unity 2022.3.0f1 or later.
- PhysioLab\ :sup:`XR` installed and running.


Installation
*************

You can install the package by following these steps:

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/PhysioLabXR_UnityPkg_GitInstallation.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

- Open the Unity project, or create a new one.
- Open the Package Manager (Window -> Package Manager).
- Click on the "+" button at the top left of the Package Manager window and select "Add package from git URL...".
- Paste the following URL: `https://github.com/PhysioLabXR/Unity-PhysioLabXR-Plugin.git <https://github.com/PhysioLabXR/Unity-PhysioLabXR-Plugin.git>`_
- Click "Add".
- The package will be installed automatically.

Check out the following example implementations, or import them directly into your project in the "Samples" section of
the installed package, and head to :ref:`this page <create lsl stream>` once you're ready to create a stream to receive
the data in PhysioLab\ :sup:`XR`.

.. _lsl data source in unity:

Lab Streaming Layer (LSL)
--------------------------

Here is an example of how to use the LSL functionality in the package to broadcast a stream of data via LSL.
A preconfigured scene is also available in the "Samples" section of the installed package, and clicking "Import"
will add it to your project directly.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/PhysioLabXR_UnityPkg_LSLExample.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

1. Create a new script in Unity, name it "LSLOutletController.cs", and replace the default contents of the script file with the following code. Alternatively, you can find the script in the "Samples" section of the installed package, and clicking "Import" will add it to your project directly.

.. code-block:: csharp

    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using LSL;

    /* A simple example of how to use LSL in Unity.
    LSL is broadly recommended for high-frequency, low-channel-count data streams such as 250Hz EEG.
    This MonoBehavior-derived class can have all default Unity methods (Start, Update, etc.) be called automatically by Unity.

    In this example, we set up a StreamInfo data object and a StreamOutlet stream object in Start().
    In each Update() (or FixedUpdate()!), we broadcast an array of arbitrary data. */
    public class LSLOutletController : MonoBehaviour
    {
        [Header("LSL Stream Settings")]
        public LSL.channel_format_t channelFormat = LSL.channel_format_t.cf_float32;
        public string streamName = "unity_lsl_my_stream_name";
        public string streamType = "LSL";
        public int channelNum = 8;
        public float nominalSamplingRate = 100.0f;

        [Header("Stream Status")]
        public StreamOutlet streamOutlet;
        public float start_time;
        public float sent_samples = 0.0f;

        // Start is called before the first frame update
        void Start()
        {
            start_time = Time.time;

            StreamInfo streamInfo = new StreamInfo(streamName,
                                                    streamType,
                                                    channelNum,
                                                    nominalSamplingRate,
                                                    channelFormat
                                                    );
            streamOutlet = new StreamOutlet(streamInfo);
        }

        // Update is called once per frame update
        void Update()
        {
            float elapsed_time = Time.time - start_time;
            int required_samples = (int)(elapsed_time * nominalSamplingRate) - (int)sent_samples;

            for (int i = 0; i < required_samples; i++)
            {
                // you can also get the channel count from streamOutlet.info().channel_count()
                float[] randomArray = new float[channelNum];
                for (int j = 0; j < channelNum; j++)
                {
                    randomArray[j] = Random.Range(0.0f, 1.0f);
                }
                // Data is broadcasted only when push_sample(data) is called
                streamOutlet.push_sample(randomArray);
            }
            sent_samples += required_samples;
        }
    }

2. Create an empty GameObject in the Unity scene, or find an existing GameObject to add this script to.
3. Drag and drop the "LSLOutletController.cs" script onto the GameObject, or add the script to the GameObject by clicking "Add Component" and searching for the script name.
4. Enter Play Mode.
5. The resulting component will broadcast a 100Hz stream with 8 channels of random data via LSL.
6. Check out :ref:`this page <create lsl stream>` on how to receive this data by listening for the stream in PhysioLab\ :sup:`XR`!

.. _zmq data source in unity:

ZeroMQ (ZMQ)
------------

.. important::
    Before we begin, please check out the :ref:`ZMQ Notes <zmq technicality>` for important information regarding ZMQ configuration.

In this example, we will show you how to write your own data source in Unity (C#) using ZMQ. However, instead of sending random data in the LSL example above, we will send camera data from Unity to PhysioLab\ :sup:`XR`. A preconfigured scene is also available in the "Samples" section of the installed package, and clicking "Import" will add it to your project directly.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/PhysioLabXR_UnityPkg_ZMQExample.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>

1. Create a new script in Unity, name it "ZMQPublisherController.cs", and replace the default contents of the script file with the following code. Alternatively, you can find the script in the "Samples" section of the installed package, and clicking "Import" will add it to your project directly.

.. code-block:: csharp

    using System.Collections;
    using UnityEngine;
    using AsyncIO;
    using NetMQ;
    using NetMQ.Sockets;
    using System;

    /* A simple example of how to use ZMQ in Unity.
    ZMQ is broadly recommended for low-frequency, high-channel-count data streams such as camera streams -- each pixel can be thought of as a channel.
    This MonoBehavior-derived class can have all default Unity methods (Start, Update, etc.) be called automatically by Unity.

    In this example, we open a ZMQ PublisherSocket and fire up a coroutine, UploadCapture, to broadcast camera images at a specified rate.
    In each iteration of the coroutine, UploadCapture, we Send some Frames of arbitrary camera data using byte[]s (byte arrays).
    In one line of code, you can chain any number of SendMoreFrame(bytes) as long as there is a final SendFrame(bytes) at the end.
    Since ZMQ uses sockets and has more going on under the hood than LSL, remember to include a cleanup function such as OnDestroy(). */
    public class ZMQPublisherController : MonoBehaviour
    {
        [Header("Scene References")]
        public Camera captureCamera;  // in your editor, set this to the camera you want to capture

        [Header("ZMQ Camera Capture Settings")]
        public int imageWidth = 400;
        public int imageHeight = 400;
        public float sendRate = 15f;

        [Header("ZMQ Networking Settings")]
        public string tcpAddress = "tcp://localhost:5557";
        public string streamName = "unity_zmq_my_stream_name";

        [Header("Stream Status")]
        public PublisherSocket socket;
        public long imageCounter = 0;

        // objects to hold the image data;
        RenderTexture tempRenderColorTexture;
        Texture2D colorImage;

        /// Start is called before the first frame update
        private void Start()
        {
            // RenderTexture is basically a block of memory (NativeArray<T> under the hood) to manually capture the camera's image later on
            tempRenderColorTexture = new RenderTexture(imageWidth, imageHeight, 32, RenderTextureFormat.ARGB32)
            {
                antiAliasing = 4
            };

            // Texture2D is the actual image data that we will send over ZMQ
            // Notice that we drop the Alpha channel (TextureFormat.RGB24 here vs RenderTextureFormat.ARGB32 in tempRenderColorTexture since Alpha doesn't make sense for a camera image)
            // In order to read this stream in PhysioLabXR, pick the "uint8" data type so that each block of 24 bits is interpreted as a 3 8-bit channels (RGB)!
            colorImage = new Texture2D(imageWidth, imageHeight, TextureFormat.RGB24, false, true);

            ForceDotNet.Force();
            socket = new PublisherSocket(tcpAddress);
            StartCoroutine(UploadCapture(1f / sendRate));
        }

        /// <summary>
        /// A coroutine that uploads an image from captureCamera every waitTime seconds.
        /// </summary>
        IEnumerator UploadCapture(float waitTime)
        {
            while (true)
            {
                yield return new WaitForSeconds(waitTime);

                double timestamp = Time.unscaledTime;
                byte[] imageBytes = EncodeColorCamera();
                socket.SendMoreFrame(streamName).SendMoreFrame(BitConverter.GetBytes(timestamp)).SendFrame(imageBytes);
                imageCounter++;
            }
        }

        /// <summary>
        /// Encodes captureCamera's image into a byte array.
        /// </summary>
        public byte[] EncodeColorCamera()
        {
            // In order to render the camera manually to tempRenderColorTexture, we must change the target texture of the camera to tempRenderColorTexture
            // targetTexture is a pointer to a RenderTexture, again a continuous block of memory optimized for the GPU, to which the camera will render
            RenderTexture prevTargetTexture = captureCamera.targetTexture; // First, save the original target (we expect this will be the screen of the application, so we want to return it after we are done!)
            captureCamera.targetTexture = tempRenderColorTexture;
            RenderTexture.active = tempRenderColorTexture;
            captureCamera.Render(); // Manually Render() once to our target, capturing the camera's image!

            colorImage.ReadPixels(new Rect(0, 0, colorImage.width, colorImage.height), 0, 0);
            colorImage.Apply();

            captureCamera.targetTexture = prevTargetTexture; // Before returning, we must reset the camera's target texture to its original value to continue rendering to the screen
            return colorImage.GetRawTextureData(); // Finally, we now return the byte[] of the image!
        }

        private void OnDestroy()
        {
            socket.Dispose();
            NetMQConfig.Cleanup();
        }

    }

2. Create an empty GameObject in the Unity scene, or find an existing GameObject to add this script to.
3. Drag and drop the "ZMQPublisherController.cs" script onto the GameObject, or add the script to the GameObject by clicking "Add Component" and searching for the script name.
4. Drag a camera reference from the scene into the "captureCamera" field in the inspector.
5. Enter Play Mode.
6. The resulting component will broadcast a 400-by-400-pixel, 15Hz stream of camera data via ZMQ.
7. Check out :ref:`this page <create zmq stream>` on how to receive this data by listening for the stream in PhysioLab\ :sup:`XR`!


gRPC (Remote Procedure Call)
-----------------------------

Please refer to the :ref:`RPC feature <feature rpc>` page for a comprehensive guide on using RPC from PhysioLabXR.

