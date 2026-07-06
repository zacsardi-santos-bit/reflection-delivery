Implement input validation for functional models to ensure required inputs are not passed as None, while allowing optional inputs to handle None gracefully. Update the Input function and related methods to enforce this behavior consistently across inference and training.

*   Update the `Input` function in `keras/src/layers/core/input_layer.py` (or re-export in `keras/src/layers/__init__.py`):
    *   Accept an `optional` boolean keyword argument (default `False`).
    *   Create a symbolic input tensor with the `optional` attribute reflecting the argument value.

*   Modify the `InputLayer` class in `keras/src/layers/core/input_layer.py`:
    *   Ensure it exposes an `optional` boolean attribute that matches the `optional` argument from the `Input` function.

*   Update the `_convert_inputs_to_tensors` method in `keras/src/models/functional.py`:
    *   Check each element of `flat_inputs`:
        *   If the value is `None` and the corresponding `InputLayer` has `optional=False`, raise a `ValueError`.
            *   Error message must include "not optional" and the input's name: "The input '{input_name}' is not optional, but None was passed. Please provide a valid tensor."
        *   If the value is `None` and `optional=True`, allow it to pass through unchanged.

*   Enhance the `_assert_input_compatibility` method in `keras/src/models/functional.py`:
    *   Iterate over flattened inputs.
    *   Raise a `ValueError` for any `None` value whose corresponding `InputLayer` has `optional=False`.
        *   Ensure the error message contains "not optional" for clarity.
    *   Apply this validation during both inference and training to propagate errors correctly.

*   Ensure that when a Functional model is called:
    *   Passing `None` for a required input raises a `ValueError` with the specified message format.
    *   Passing `None` for an optional input results in a successful forward pass and correct output.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.