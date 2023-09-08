###################################
Real-time Digital Signal Processing
###################################

PhysioLab\ :sup:`XR` offers a comprehensive selection of built-in digital signal processing (DSP) modules.
These modules encompass commonly utilized signal processing algorithms,
including low-pass filters, high-pass filters, band-pass filters,
root mean square calculations, and clutter removal techniques .
The parameters for these digital filters are generated using the `SciPy <https://scipy.org/>`_ library.
and applied to the rational transfer function defined as follows and optimized with circular buffers and `Cython <https://cython.org/>`_.

    The source code is located at `dsp_module <https://github.com/PhysioLabXR/PhysioLabXR/blob/master/physiolabxr/utils/dsp_utils/dsp_modules.py>`_.

Rational Transfer Function
--------------------------

Real-time digital filters are implemented using the following rational transfer function:

.. math::

   Y(z) = \frac{b(1) + b(2)z^{-1} + \ldots + b(n_b + 1)z^{-n_b}}{1 + a(2)z^{-1} + \ldots + a(n_a + 1)z^{-n_a}} \cdot X(z)

and the graphic representation is as follows:

.. image:: media/DSP-GraphicRepresentation.png
   :width: 100%
   :align: center




where :math:`X(z)` is the input signal, :math:`Y(z)` is the output signal, :math:`n_b` is the number of zeros,
:math:`n_a` is the number of poles, and :math:`b` and :math:`a` are the coefficients of the numerator and denominator,
respectively. The a and b coefficients are calculated using the `SciPy <https://scipy.org/>`_ library. You can refer to the `matlab
1-D filter. <https://www.mathworks.com/help/signal/ref/filter.html>`_ for more detail about the math behind the filter.


Filter Types
--------------------------

PhysioLab\ :sup:`XR` offers the following filter types:


* NotchFilter: to remove a specific frequency from the signal.
 Configurable parameters:
    * w0/freq (float): the frequency to remove.
    * Q/quality factor (float): the quality factor.
    * fs (float): the sampling frequency.

* ButterworthLowpassFilter: to remove high frequency components from the signal.
 Configurable parameters:
    * cutoff (float): the cutoff frequency.
    * fs (float): the sampling frequency.
    * order (int): the order of the filter.


* ButterworthHighpassFilter: to remove low frequency components from the signal.
 Configurable parameters:
    * cutoff (float): the cutoff frequency.
    * fs (float): the sampling frequency.
    * order (int): the order of the filter.

* ButterworthBandpassFilter: to remove low and high frequency components from the signal.
 Configurable parameters:
    * low_cutoff (float): the low cutoff frequency.
    * high_cutoff (float): the high cutoff frequency.
    * fs (float): the sampling frequency.
    * order (int): the order of the filter.



* RootMeanSquare: to calculate the root mean square of the signal over time.
 Configurable parameters:
    * window (float): the window size in milliseconds.
    * fs (float): the sampling frequency.


* ClutterRemoval: statistic method to remove the clutter from the signal. The algorithm is based on the following paper: `IndexPen <https://dl.acm.org/doi/abs/10.1145/3534601>`_.
 Configurable parameters:
    * signal_clutter_ratio (float): the signal to clutter ratio.


