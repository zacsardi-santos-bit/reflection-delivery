## Description

The built-in linear spacing operation in TensorFlow currently only supports scalar start and stop values, which limits its usefulness for multi-dimensional workflows. Developers frequently need to generate evenly-spaced sequences between multiple pairs of endpoints at once — for example, interpolating between two vectors element-wise, building batched coordinate grids, or creating parallel sequences across a batch dimension. With the current scalar-only restriction, these use cases require manual looping or custom workarounds.

## Expected Behavior

- The linear spacing function should accept multi-dimensional tensors for both start and stop, generating the interpolated sequence along a user-specified axis.
- An axis parameter should control which dimension of the output holds the evenly-spaced values, with a default of 0. Both positive and negative axis indices should be supported.
- Scalar inputs should continue to work as before and match the behavior of the standard reference implementation for linear spacing.
- The first and last output values must exactly equal the start and stop inputs — not approximate them — so that endpoint precision is guaranteed regardless of the number of steps.
- The number of steps parameter should accept dynamically-determined values (e.g., tensors whose values are only known at runtime), not just statically-known Python integers.
- The function should handle cases where the shapes of start and stop are not fully known at graph construction time.

## Why This Matters

Without multi-dimensional support, any workflow that needs to simultaneously interpolate across a set of start/end pairs cannot use the standard utility and must rely on fragile custom implementations. Adding this capability directly into the core operation makes batch interpolation first-class, reduces boilerplate, and ensures numerical correctness at endpoints.
