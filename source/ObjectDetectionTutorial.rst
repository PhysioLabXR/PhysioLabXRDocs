##############################
WebCam Object Detection
##############################


(teaser video)

In this tutorial, you will run a simple object detection algorithm on the image stream from your webcam.

The algorithm will detect objects in the image and draw bounding boxes around them.
It will also output the classes of the objects detected.
The algorithm is based on the `MobileNetV3 <https://openaccess.thecvf.com/content_ICCV_2019/html/Howard_Searching_for_MobileNetV3_ICCV_2019_paper.html>`_, and the model was trained on the `COCO dataset <https://cocodataset.org/#home>`_.

You will need a webcam hooked to your computer to try this tutorial.


Connect the WebCam
************************

Plug in your webcam to your computer.

Open PhysioLab\ :sup:`XR` and make sure you are on the ``Stream`` tab.

PhysioLab\ :sup:`XR` automatically detects the video input devices
connected to your computer. Their name will be listed in the **Add Stream** dropdown as **Camera 'x'**.
To add your webcam as an video input stream:

#. Click on the drop down of **Add Stream**  and select the video device you want to add. If the webcam is the only video device connected to your computer, it should be listed as **Camera 0**.
#. Click on **Add** button. This may take a minute to load.
#. You should see the video stream from the webcam.
#. Check if the color is consistent with the real world. If it is not, click on the **settings** button on the bottom and change the channel order until the color is consistent with the real world.


.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <video id="autoplay-video1" autoplay controls loop muted playsinline style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
            <source src="_static/video_device.mp4" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>


Download the Object Detection Model
************************

Download the object detection model and the user script from `here <https://drive.google.com/file/d/1WLWCxxepP8b2Y50280sZJzJ0aeCklE13/view?usp=share_link>`_.

Unzip the folder in a location that you can easily access.

To add this user script:

#. Go to the ``Scripting`` Tab and click on **Add** button to add a script.
#. To find the user script you just downloaded, click on **Locate** button.
#. Navigate to the folder you just unzipped and select the file *ObjectDetection.py*. Click **Open**. You should see the script loaded.

(video)


Here is the code for the user script:

    .. code-block:: python

        import warnings

        import os
        import cv2
        import zmq
        import numpy as np
        from physiolabxr.scripting.RenaScript import RenaScript

        # get the object detection model
        def get_od_model(config_path, weights_path, input_size):
            net = cv2.dnn_DetectionModel(weights_path, config_path)
            net.setInputSize(input_size)
            net.setInputScale(1.0 / 127.5)
            net.setInputMean((127.5, 127.5, 127.5))
            net.setInputSwapRB(True)
            return net

        # get the class names from the coco.names file for the object detection model
        def get_class_names(class_file):
            with open(class_file, 'rt') as f:
                class_name = f.read().rstrip('\n').split('\n')
            return class_name


        # process the received camera images
        def process_received_camera_images(image_data, net, class_names, image_shape, threshold=0.45, nms_threshold=0.2):
            color_img = image_data.reshape(image_shape).astype(np.uint8) # reshape the image data to the image shape
            color_img = cv2.rotate(color_img, cv2.ROTATE_90_COUNTERCLOCKWISE) # rotate the image 90 degrees counter clockwise because the cv2 has a different origin

            classIds, confs, bbox = net.detect(color_img, confThreshold=threshold) # get the bounding boxes, confidence, and class ids
            bbox = list(bbox)
            confs = list(np.array(confs).reshape(1, -1)[0])
            confs = list(map(float, confs))

            indices = cv2.dnn.NMSBoxes(bbox, confs, threshold, nms_threshold) # get the indices of the bounding boxes
            detected_classes, xs, ys, ws, hs = list(), list(), list(), list(), list()

            for i in indices:
                class_id = classIds[i][0] if type(classIds[i]) is list or type(classIds[i]) is np.ndarray else classIds[i]
                i = i[0] if type(i) is list or type(i) is np.ndarray else i
                box = bbox[i]
                x, y, w, h = box[0], box[1], box[2], box[3]
                xs.append(int(x))
                ys.append(int(y))
                ws.append(int(w))
                hs.append(int(h))

                # Yolo 2D bb visualization
                detected_classes.append(int(class_id))
                cv2.rectangle(color_img, (x, y), (x + w, h + y), color=(0, 255, 0), thickness=2)
                cv2.putText(color_img, class_names[class_id - 1].upper(),
                            (np.max((0, np.min((image_shape[0], box[0] + 10)))),
                             np.max((0, np.min((image_shape[1], box[1] + 30))))),
                            cv2.FONT_HERSHEY_COMPLEX, 1, (0, 255, 0), 2)

            color_img = cv2.rotate(color_img, cv2.ROTATE_90_CLOCKWISE) # rotate the image back to its original orientation

            # return the detected classes, the positions, and the image with bounding boxes
            return {
                'classIDs': detected_classes,
                'xs': xs,
                'ys': ys,
                'ws': ws,
                'hs': hs,
            }, color_img

        class ObjectDetectionScript(RenaScript):
            def __init__(self, *args, **kwargs):
                """
                Please do not edit this function
                """
                super().__init__(*args, **kwargs)

                current_directory = os.path.dirname(__file__)
                config_path = os.path.join(current_directory, 'ssd_mobilenet_v3_large_coco_2020_01_14.pbtxt')
                weights_path = os.path.join(current_directory, 'frozen_inference_graph.pb')
                self.image_shape = (640, 480, 3)
                self.ob_model = get_od_model(config_path, weights_path, input_size=self.image_shape[:2])
                self.class_names = get_class_names(os.path.join(current_directory, 'coco.names'))


            # Start will be called once when the run button is hit.
            def init(self):
                pass

            # loop is called <Run Frequency> times per second
            def loop(self):
                camera_stream_names = [x for x in self.inputs.keys() if x.startswith("Camera")]
                if len(camera_stream_names) > 0: # check if the camera is in the inputs
                    # take the first stream whose name starts with camera
                    stream_name = camera_stream_names[0]
                    image_data = self.inputs[stream_name][0][:, -1] # get the newest image data from the camera
                    detected_pos, img_w_bbx = process_received_camera_images(image_data, self.ob_model, self.class_names, self.image_shape) # process the image data
                    self.outputs["OutputImg"] = img_w_bbx.reshape(-1) # reshape the output image to send
                    self.inputs.clear_buffer() # clear the input buffer


            # cleanup is called when the stop button is hit
            def cleanup(self):
                print('Cleanup function is called')


Running the user script
************************

To run this user script:

#. In ``Inputs``, type in **Camera 'x'** and click on the **Add** button. This will add the webcam stream as an input. The user needs to type this in everytime they reload the script for reasons described in :ref:`Video Device API <datastreamapi video devices>`
#. In ``Outputs``, type in **OutputImg** and click on the **Add** button. We use this name because we named the output that way in the code. Please feel free to play around with it. Adding this here will enable streaming of the output image in the user code.
#. Change the size of the OutputImg to **921600**. This is the size of the image stream from the webcam, which is 640x480x3.
#. Make sure you select **ZMQ** as the streaming interface and **uint8** as its datatype. You can use any port number, but here we chose **11000** for the example.
#. Go back to the ``Stream`` tab. In ``Add Stream``, type in **OutputImg**, make sure you are using the same port number as the one you chose previously. Double check that your datatype is **uint8** and that you are using **ZMQ** as the streaming interface. Then, click on the **Add** button. This will add the output image of the user code as a stream. Please notice that the ``Stream`` tab and the user script are independent from each other, and that adding something in one place does not automatically add it to the other.

Now, you are ready to run the code. Go back to the ``Scripting`` tab and click on the ``Run`` button.

(video)

Visualize the Output Image Stream
************************

To see the output image stream from the webcam with object detection boxes:

#. Go back to the ``Stream`` tab. Click the **Start All** button to start all the streams or click the **Play** button under the OutputImg stream to start only that stream.
#. Adjust the scale of the output image stream.
#. Click on the ``settings`` button on the bottom of the OutputImg Stream, click on ``default group name``, and check that the width and height are **640** and **480** respectively. Also, select **rgb** under ``Image``.
#. Drag down the horizontal bar, make sure the channel format is selected as **channel last**.
#. Close the settings window. Go back to the Stream and click on the letter ``A`` in the lower left corner to see the whole picture. You should see the webcam image stream with object detection boxes.

(video)