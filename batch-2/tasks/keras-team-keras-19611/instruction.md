Implement the necessary changes to the CTC operations in Keras to address usability issues and ensure consistency across backends. Update the functions and classes as specified to improve the interface and functionality.

*   Update `ctc_decode` function in `keras/src/ops/nn.py`:
    *   Default `strategy` parameter to `"greedy"`.
    *   Raise `ValueError` with message containing `"Invalid strategy"` for unsupported strategies.
    *   Ensure `decoded` output is of dtype `int32` and `scores` output is of dtype `result_type(input_dtype, "float32")`.
    *   Set output shapes: `decoded` as `(effective_top_paths, batch_size, inputs.shape[1])` and `scores` as `(batch_size, effective_top_paths)`.
    *   Use `-1` for padding unfilled positions in `decoded` output.
    *   Merge consecutive duplicate labels when `merge_repeated=True`.

*   Implement `CTCDecode` class in `keras/src/ops/nn.py`:
    *   Accept `strategy` as a constructor argument.
    *   Provide `symbolic_call(inputs, sequence_lengths)` method returning `(decoded, scores)`.
    *   Ensure `decoded` dtype is `int32` and `scores` dtype is `result_type(input_dtype, "float32")`.
    *   Support both static and dynamic input dimensions.

*   Rename `CtcLoss` class to `CTCLoss` in `keras/src/ops/nn.py`:
    *   Ensure it is accessible as `knn.CTCLoss`.
    *   Support `mask_index` parameter in constructor, defaulting to `0`.
    *   Implement `._check_shape_first_dim(name1, shape1, name2, shape2)` method to raise `ValueError` for mismatched first dimensions.
    *   Implement `.symbolic_call(target, output, target_length, output_length)` method with appropriate dtype handling.

*   Update `ctc_loss` function in `keras/src/ops/nn.py`:
    *   Ensure compatibility with the numpy backend.
    *   Return dtype `float32` for `float16` or `bfloat16` inputs, otherwise use `result_type(output.dtype, "float32")`.

*   Implement numpy backend support in `keras/src/backend/numpy/nn.py`:
    *   Implement `ctc_decode` function:
        *   Default `strategy` to `"greedy"`.
        *   Validate `strategy` and raise `ValueError` for unsupported values.
        *   Return `(decoded, scores)` with `decoded` dtype `int32` and `scores` dtype `result_type(input_dtype, "float32")`.
        *   Use `-1` for padding unfilled positions.
    *   Implement `ctc_loss` function:
        *   Ensure it works without raising `AttributeError`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.