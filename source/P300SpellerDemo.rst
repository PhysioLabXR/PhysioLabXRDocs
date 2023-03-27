


#############
P300 Speller Game
#############

    Last Modified: 3/27/2023

.. contents:: Overview

*************
Introduction
*************

This document describes the P300 Speller demo using the RenaLabApp Scripting function and a customized Unity paradigm.
The setup of this experiment can be extended to other real-time experiments that include Unity and real-time
bioinformation acquisition devices.
The purpose of this demo is to demonstrate the flexibility of RenaLabApp and
take a glance on how to customize your own experiment paradigm.

For the detailed software implementation, please check the source script (P300SpellerScript.py) and Unity Repository.

    **Note**: Please refer to the RenaLabApp Setup page and Scripting Page for downloading the software and basic functionalities of Scripting.






*************
Unity Paradigm
*************

This Unity paradigm implements a classic P300 Speller BCI game and researchers can use it for other testing purposes.

=======
Lab Streaming Layer (LSL) Configuration
=======
There is a 6 channel LSL stream from Unity Paradigm to RenaScript and the description for each channel is shown below:

P300 Speller Game Unity  LSL  Configuration:

    **LSL Outlet: "P300Speller"**

        *Channel[0]: (P300SpellerGameStateControlMarker)*
            Indicates the selected game states (Train/Test/Interrupt)
                | StartTrainingEventMarker: 1
                | EndTrainingEventMarker: 2
                | StartTestingEventMarker: 3
                | EndTestingEventMarker: 4
                | InterruptExperimentMarker: -1


        *Channel[1]: (P300SpellerTrailStartEndMarker)*
            Indicates the start and end of flashing for each trail
                | TrailStartMarker: 1
                | TrailEndMarker: 2


        *Channel[2]: (P300SpellerStartFlashingMarker)*
            The flash event marker. Send 1 while any column or row turns on
                | FlashingMarker: 1

        *Channel[3]: (P300SpellerFlashingRowOrColumMarker)*
            Indicate the identity of the flashing component
                | RowFlashingMarker: 1
                | ColFlashingMarker: 2



        *Channel[4]: (P300SpellerFlashingRowColumIndexMarker)*
            Indicate the index of the flashing component. The number represents the (index+1) of the flashing component.
                | Row Index: 1 - 6
                | Column Index: 1 - 5


        *Channel[5]: (P300SpellerTargetNonTargetMarker)*
            Indicate if the component is target or non-target during the training process. This channel is zero during the testing mode.
                | NonTargetEventMarker: 1
                | TargetEventMarker: 2

*Note*:None of the event markers should be zero,
because the LSL pulls zero frames while there is no data,
and the stimulation channel in MNE library uses zero to represent no event.
Additionally, the unused channel will be set to 0 for all events.
Therefore, we add 1 to the stream for anytime we want to send the index of something.
For example, the row index marker will be 1 to 6.


=======
State Diagram
=======

Practice
************

.. image:: media/ScriptingDemo/P300Speller/UnityPracticeDiagram.png
    :width: 1080


Train
************
.. image:: media/ScriptingDemo/P300Speller/UnityPracticeDiagram.png
    :width: 1080


Test
************
.. image:: media/ScriptingDemo/P300Speller/UnityPracticeDiagram.png
    :width: 1080


    Please Refer to the source code for more detailed implementation.



*************
Rena Script: P300SpellerScript.py
*************

The RenaScript implemented a basic real-time training and testing pipeline for the P300 Speller game for Unity with a basic logistic regression.

=======
Lab Streaming Layer (LSL) Configuration
=======

There is a 2 channel LSL stream from RenaScript to Unity and the description for each channel is shown below:

P300 Speller Game RenaScript LSL Configuration:

    **LSL Outlet: "P300SpellerRenaScript"**

        *Channel0: (P300SpellerScriptPostProcessingDoneMarker)*
            Indicate the post processing has been done in RenaScript
                | FinishedTrainingMarker: 1
                | FinishedTestingMarker: 2


        *Channel1: (P300SpellerScriptPostProcessingInformationMarker)*
            Indicate the information from the post processing
                | TrainingAccuracy: The accuracy from the training (value range: 0 - 1)
                | PredictedCharacterIndex: The index+1 of the prediction result (value range: 1 - 30)

=======
State Diagram
=======



*************
Experiment
*************

This experiment implemented the P300 Speller Brain Computer Interface using RenaLabApp and a customized Unity Paradigm.
The similar setup can be extended to other human subject studies that include real time data acquisition and customized
experiment environment. The P300 is one of the most important Event-related potentials(ERP)
components that is used to evaluate cognitive function, such as attention, working memory , and concentration.
A peak occurs 300 ms after the expected event happened.

The Unity Paradigm hosts a six by five board that includes 26 alphabets, from A to Z,
as well as 4 instruction characters, including space, backspace, enter and activation. This input format adapts our previous publication IndexPen: Two-Finger Text Input with Millimeter-Wave Radar in which also used RenaLabApp for the entire user study.

During the training period, the user is instructed to focus on a particular letter(instructed in green),
and each roll and column will be flashed 15 times; therefore, there are 15 x 11 samples for each trail.
After the training mode, the user can go to the testing mode. Similarly, the user will focus on one character
during each trail, but without instruction. The predicted result will be typed in the text input box.

=======
Requirements
=======

    1. RenaLabApp
    2. Unity: https://github.com/HaowenWeiJohn/RenaLabApp-Unity-P300Speller
    3. Hardware: OpenBCI Cyton (8 Channels)
        Channel Selection: Fz, Cz, Pz, C3, C4, P3, P4, O1.

Both **RenaScript** and **Unity** act as a state machine that follows the following state diagrams.
The communication between two applications were done by using LabStreamLayer(LSL)
which transform the event markers and synchronize the data stream and event streams.

TODO: Sequentail Diagram


=======
Experiment Setup
=======


#. The first step is to set up the OpenBCI Cyton board. You can follow the instructions on https://docs.openbci.com/ . In our case we used Fz, Cz, Pz, C3, C4, P3, P4, O1. You can use OpenBCI GUI to check the signal quality before the experiment. To access the Cyton board stream, you can either create a customized script to get the data and export the data to LSL similar to the OpenBCIInterface.py example or use the supported device driver in RenaLabApp. Please check out the Hardware Support page for more information about supported devices in RenaLabApp.

#. Open the game through P300Speller.exe or Unity Editor.

#. Download and open the RenaLabApp.
#. Add the "P300Speller" event marker stream from P300 Speller game.
#. Add the OpenBCI stream from your customized script or serial driver in RenaLabApp.
#. Open Both streams. (You should see the play buttons turn green if the stream exists).
#. Add a new scripting widget in the RenaScript page.
#. Select the P300Script.py (TODO: file location) in your local computer.
#. Add both input streams in the scripting widget.
#. Change the buffer size and run times to a reasonable value. (We recommend 3 seconds for the buffer size and 5 for the run frequency.)
#. Click the Run button to run the script

=======
Run Experiment
=======

Practice
    He/She can practice the experiment in the Practice mode which does not interact with the RenaScript.

Train
    The first step is to collect some training data for the Logistic Regression model. The default training session asks the user to spell { "R"  "E" "N"  "A" }, and this can be modified in the options window.  The flashing pattern will be the same as the Practice Mode. After each trial, the Unity interface will wait for the training finish signal from RenaLabApp Script and the training accuracy will be shown on the screen. (The timeout duration will be 2 seconds by default).

Test
    After the training stage, he/she can test the trained model by clicking the test button. The test trial will run recursively until the user clicks the interrupt button.Unity will wait for the prediction result from RenaLabApp at the end of each trial, and the board will show the prediction result as well as the text input box on top. (The timeout duration will be 2 seconds by default, same as the training mode ).



*Run the experiment without OpenBCI device and Unity*
    We provide users with a recorded experiment that can be replayed while the user starts the replay.

