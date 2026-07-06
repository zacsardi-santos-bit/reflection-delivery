## Description

Several tensor operations in Keras do not validate axis arguments during shape inference (when building models with symbolic tensors). When a developer specifies an out-of-range axis, no error is raised at model-build time — the mistake only surfaces at runtime, making debugging much harder. Additionally, there is no shared utility for canonicalizing multiple axes at once (normalizing negative indices, validating types), so each operation handles this ad hoc or not at all.

## Expected Behavior

- A new shared utility function should normalize a single axis or a collection of axes (given as a list or tuple) to a tuple of non-negative integers, handling negative indices correctly.
- The utility should accept both built-in integer types and integer-like types from numeric computing libraries as valid axis inputs.
- The utility should raise a clear type error when the axis argument is a non-integer or a sequence containing non-integer elements.
- Operations that perform dimension-specific work (softmax variants, array flip, array roll, trace) should validate their axis arguments during shape inference and raise a clear value error for out-of-range values.
- The trace operation should additionally raise an error when both trace axes refer to the same dimension.
- The sparse categorical cross-entropy operation should correctly compute the output shape when the class dimension is specified at any axis position, not just the last dimension.

## Why This Matters

Without axis validation during shape inference, bugs caused by wrong axis values in large models are delayed and harder to trace. Enforcing axis bounds early makes model construction safer and gives developers immediate, actionable feedback. Supporting axis lists in flip and roll operations, and any-axis class dimensions in cross-entropy, also makes these operations more flexible for non-standard tensor layouts.
