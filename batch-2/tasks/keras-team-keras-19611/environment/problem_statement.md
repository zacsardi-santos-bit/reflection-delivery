## Description

The CTC (Connectionist Temporal Classification) operations in Keras have several issues that limit their usability across backends and create inconsistencies in their interface.

**Issues:**

1. **No numpy backend support**: Both the CTC loss computation and the CTC decoder raise errors when running under the numpy backend. Users who rely on the numpy backend for testing or prototyping cannot use these operations at all.

2. **Required strategy argument**: The decode function requires users to explicitly pass a decoding strategy even when they simply want the default greedy behavior. The greedy approach should be the default so it does not have to be specified every time.

3. **Wrong padding value for decoded outputs**: The decoded output uses zero to fill unused positions beyond the decoded sequence length. The conventional and expected fill value is negative one, which distinguishes "no label" from the first valid label index.

4. **Naming inconsistency**: The loss operation class uses a different capitalization convention than the rest of the neural network operations. It should be renamed to follow the consistent casing style used by other operations in the same module.

5. **Missing input validation**: When an unrecognized decoding strategy is passed, there is no clear error message — the call either silently misbehaves or raises an obscure error. A descriptive validation error should be raised immediately.

## Expected Behavior

- CTC loss and CTC decode should work with the numpy backend.
- The decoding function should default to the greedy strategy when no strategy is specified.
- Decoded outputs should use negative one as the padding fill value for positions beyond the decoded sequence length.
- The loss operation class should be accessible under the corrected naming convention.
- Passing an unsupported strategy name should raise a clear validation error indicating the strategy is invalid.
- Decoded labels should always be returned as integer tensors; scores should be floating point with appropriate dtype promotion, ensuring at least 32-bit floating point precision.
- The decode operation should support symbolic tensor inputs, with shape inference correctly computing the output shapes based on the input dimensions and the number of top paths.

## Why This Matters

These inconsistencies make the CTC operations error-prone and incomplete. Fixing them ensures that CTC-based models (such as speech recognition and handwriting recognition) work reliably across all backends and behave predictably with respect to output dtypes and padding conventions.
