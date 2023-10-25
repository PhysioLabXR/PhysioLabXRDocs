###################################
Balance Ball Tutorial
###################################
    Last Modified: 10/19/2023

.. contents:: Overview

*************
Introduction
*************

This tutorial will show you how to use PhysioLab\ :sup:`XR` and Unity to set up a simple balance ball game that
you can play by only imagining your left or right hand movements. Similar to the P300 Speller tutorial,
In this tutorial we will use the PhysioLabXR as a real-time data acquisition platform and analysis tool, and
use Unity as a stimulus presentation platform.

.. figure:: media/BBall_play.png
   :width: 400
   :align: center
   :alt: balance ball

*************
Unity Paradigm
*************

The Unity source code is available at: XXXX

.. note::
    The Unity Paradigm is developed using `Unity 2021.3.27f1 <https://unity3d.com/get-unity/download/archive>`_


This Unity paradigm implements a balance ball BCI game. After a quick training session, The user will
be able to play the game with a BCI device in real time.


=======
Lab Streaming Layer (LSL) Configuration
=======
There is a 1 channel LSL stream from Unity Paradigm to RenaScript. The description for each channel is shown below:

P300 Speller Game Unity  LSL  Configuration:

    **LSL Outlet: "XXX"**

        *Channel[0]: (EventMarker_BallGame)*
            Indicates the selected game states (Train/Trial/Evaluation)
                - TrainStart = 1, TrainEnd = -1
                - LeftHandTrialStart = 2, LeftHandTrialEnd = -2
                - RightHandTrialStart = 3, RightHandTrialEnd = -3
                - LeftBlockStart = 7, LeftBlockEnd = -7
                - RightBlockStart = 8, RightBlockEnd = -8
                - EvalStart = 6, EvalEnd = -6


=======
State Diagram
=======


Train State
************


*************
PhysioLab\ :sup:`XR` Scripting
*************


=======
Lab Streaming Layer (LSL) Configuration
=======

There is a 1 channel LSL stream from the PhysioLab\ :sup:`XR` to Unity to transfer the predicted side of hand movements from the PhysioLab\ :sup:`XR` to Unity.

PhysioLab\ :sup:`XR` LSL Configuration:

    **LSL Outlet: "XXXXX"**

        *Channel[0]: (XXXXX)*
            Indicates the predicted side of hand movements (Left/Right)
                - Left = 1
                - Right = 2



=======
Script: MotorImageryBalanceBall.py
=======

The script can be found at: `MotorImageryBalanceBall.py <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/scripting/Examples/PhysioLabXR_Balanceball_Demo/MotorImageryBalanceBall.py">`_

The configuration file includes the following configurations:

    * **EEG_STREAM_NAME**: The name of the EEG stream.
    * **EVENT_MARKER_CHANNEL_NAME**: The name of the event marker channel.


*************
Experiment
*************

This experiment implemented the Balance Ball Brain Computer Interface using RenaLabApp and a customized Unity Paradigm.
The similar setup can be extended to other human subject studies that include continuous stream of real time EEG data and customized
experiment environment.

[NEED TO ADD INFO ON THE DATA & ROI]

In the training session, the user will be asked to imagine left or right hand movements while
looking at an automated ball moving on the screen. After the training session, Unity will communicate with the python
script XXXX to fit a predictive model based on the training data.

The balance ball game has the following features implemented:
- a score counter that keeps track of the remaining lives of the ball (3 lives)
- a rectangular flat plane
- a ball that will naturally roll down to the side of the plane that is being pressed. When the ball
    reaches the edge of the plane, it will fall off and deduct one life from the player
- randomly spawned black collectible cubes that the ball can pick up by rolling over

=======
Requirements
=======

1. PhysioLab\ :sup:`XR`: `physiolabxr <https://github.com/PhysioLabXR/PhysioLabXR/tree/master>`_
2. Unity project download from: `PhysioLabXR_Balance_Ball_Demo <https://xxxx>`_
3. [NEED TO CHECK] OpenBCI: `Cyton-8-Channel <https://shop.openbci.com/collections/frontpage/products/cyton-biosensing-board-8-channel?variant=38958638540>`_
    Channel Selection: Fz, Cz, Pz, C3, C4, P3, P4, O1.