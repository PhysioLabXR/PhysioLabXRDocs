###################################
Real-time Performance
###################################

PhysioLab\ :sup:`XR` is a performs many real-time tasks. It essentially implements an real-time operating system (RTOS)
between its the incoming data stream and processing subroutines. By real-time, we emphasize on the ability of processing
subroutines able to finish their tasks in time for the next incoming data. This is a critical feature for many applications
in the field of physiological signal processing, when the data source emits data at a certain rate for which the downstream
system must be able to match.

For users for whom performance matters, this page will help you
understand the performance metrics of PhysioLab\ :sup:`XR`, its limitations, and in turn optimize the system you are building
with PhysioLab\ :sup:`XR`.

Benchmarks
**********

We benchmarked real-time features in PhysioLab\ :sup:`XR`.
Results here presented are obtained from a system with these specifications:
Windows 11 Pro, AMD Ryzen 9 5950X 3.4 GHz CPU, 128GB memory, and RTX 3090 GPU.
PhysioLab\ :sup:`XR`'s performance can varies based on the hardware configuration,
but the general trend remains the same (e.g., when a stream has a larger number of channels, the visualization FPS
is lower). When using PhysioLab\ :sup:`XR`, whether for visualization, recording, or more advance uses such as building
pipeline through the scripting interface, users are welcome to consult results here to optimize for their use case.

Visualization FPS
