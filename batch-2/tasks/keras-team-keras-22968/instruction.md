Implement enhanced error messaging in the `assert_input_compatibility` function to provide clearer guidance when inputs do not match the expected specifications for Functional models. Ensure that error messages specify the expected input format and count, improving user understanding and reducing debugging time.

*   Update the `assert_input_compatibility` function located in `keras/src/layers/input_spec.py` to handle input validation:
    *   For models expecting a single input:
        *   Raise a `ValueError` with a message matching the pattern 'expects 1 .*input' when multiple inputs are provided.
    *   For models expecting multiple positional inputs:
        *   Raise a `ValueError` with a message matching the pattern 'expects 2 .*input' when the wrong number of inputs is provided.
    *   For models expecting named dictionary inputs:
        *   Raise a `ValueError` with a message containing 'named input(s) with keys' when a non-dictionary input is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.