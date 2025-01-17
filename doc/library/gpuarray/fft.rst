.. _libdoc_gpuarray_fft:

=====================================================
:mod:`aesara.gpuarray.fft` -- Fast Fourier Transforms
=====================================================

Performs Fast Fourier Transforms (FFT) on the GPU.

FFT gradients are implemented as the opposite Fourier transform of the output gradients.

.. note ::
    You must install `scikit-cuda <http://scikit-cuda.readthedocs.io/en/latest>`_
    to compute Fourier transforms on the GPU.


.. warning ::
    The real and imaginary parts of the Fourier domain arrays are stored as a pair of float32
    arrays, emulating complex64. Since aesara has limited support for complex
    number operations, care must be taken to manually implement operations such as gradients.

.. automodule:: aesara.gpuarray.fft
   :members: curfft, cuirfft

For example, the code below performs the real input FFT of a box function, which is a sinc function.
The absolute value is plotted, since the phase oscillates due to the box function being
shifted to the middle of the array. The Aesara flag ``device=cuda{0,1...}`` must be used.

.. testcode::

    import numpy as np
    import aesara
    import aesara.tensor as at
    from aesara.gpuarray import fft

    x = at.matrix('x', dtype='float32')

    rfft = fft.curfft(x, norm='ortho')
    f_rfft = aesara.function([x], rfft)

    N = 1024
    box = np.zeros((1, N), dtype='float32')
    box[:, N/2-10: N/2+10] = 1

    out = f_rfft(box)
    c_out = np.asarray(out[0, :, 0] + 1j*out[0, :, 1])
    abs_out = abs(c_out)

.. testoutput::
   :hide:
   :options: +SKIP

   ...

.. image:: plot_fft.png
