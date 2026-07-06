Implement an extended version of TensorFlow's linear spacing operation to support multi-dimensional tensor inputs for start and stop values. Ensure the function interpolates between these values along a specified axis, with precise endpoint values and dynamic handling of the number of steps.

*   Implement the function `linspace_nd` in `tensorflow/python/ops/math_ops.py` with the following signature:
    *   `linspace_nd(start, stop, num, name=None, axis=0)`

*   Ensure scalar inputs:
    *   Accept scalar float `start` and `stop` values.
    *   Accept an integer `num`.
    *   Return a 1-D tensor of shape `[num]` with evenly-spaced values, matching `numpy.linspace` output within a tolerance of `1e-5`.

*   Guarantee endpoint precision:
    *   Ensure the first element of the output exactly equals `start`.
    *   Ensure the last element exactly equals `stop`, with no floating-point errors.

*   Support N-dimensional tensor inputs:
    *   Accept N-dimensional tensors for `start` and `stop` with matching shapes and dtypes.
    *   Return an (N+1)-dimensional tensor where the interpolation sequence runs along the specified `axis`.

*   Handle the `axis` parameter:
    *   Default `axis` to 0.
    *   Accept both positive and negative integer values for `axis`.
    *   Allow negative values to index from the end of the output shape.

*   Handle the `num` parameter:
    *   Accept a TensorFlow constant tensor for `num`, not just a Python integer.
    *   Ensure consistent results whether `num` is a constant tensor or a plain integer.

*   Support dynamic shapes:
    *   Ensure functionality when `start` and `stop` are placeholder tensors with unknown shapes at graph construction time.
    *   Handle shapes specified as concrete integers, shapes with `None` dimensions, or completely unknown shapes (`None`).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.