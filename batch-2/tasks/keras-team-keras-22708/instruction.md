Implement a utility function to normalize axis arguments for tensor operations in Keras, ensuring early validation during shape inference. Update specific operations to validate axis arguments and handle errors appropriately.

*   Implement `canonicalize_axes(axis, num_dims)` in `keras/src/backend/common/backend_utils.py`:
    *   Accept a single integer or a list/tuple of integers.
    *   Normalize negative indices to non-negative equivalents using `num_dims`.
    *   Return a tuple of non-negative integers.
    *   Raise `TypeError` if `axis` is a non-integer or if any element in a sequence is non-integer.

*   Update the following operations to validate axis arguments during shape inference:
    *   `log_softmax` and `sparsemax`:
        *   Validate `axis` against the tensor's dimensions.
        *   Raise `ValueError` for out-of-range axes.
    *   `flip` and `roll`:
        *   Validate all axes against the tensor's dimensions.
        *   Accept a list of axes if all are valid.
        *   Raise `ValueError` for any out-of-range axis.
    *   `trace`:
        *   Raise `ValueError` if `axis1` and `axis2` are the same after normalization.
        *   Raise `ValueError` for out-of-range axes.
    *   `sparse_categorical_crossentropy`:
        *   Use `axis` to determine the class probabilities dimension.
        *   Compute output shape excluding the class dimension at the specified axis.
        *   Raise `ValueError` for out-of-range axes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.