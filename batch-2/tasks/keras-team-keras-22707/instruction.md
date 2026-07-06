Implement input validation for the image padding and cropping operations in Keras to ensure clear and descriptive error messages are raised for invalid inputs. Update the `pad_images` and `crop_images` functions, as well as the `PadImages` and `CropImages` classes, to perform these validations.

Requirements:

*   Update `pad_images` function in `keras/src/ops/image.py`:
    *   Ensure input image tensor rank is 3 or 4; raise `ValueError` otherwise.
    *   Validate that exactly two of `top_padding`, `bottom_padding`, and `target_height` are non-None; raise `ValueError` with "Must specify exactly two of top_padding, bottom_padding, target_height" if not.
    *   Validate that exactly two of `left_padding`, `right_padding`, and `target_width` are non-None.
    *   Raise `ValueError` with "top_padding must be >= 0" if `top_padding` is negative or derived negative.
    *   Raise `ValueError` with "target_height must be >= 0" if `target_height` is negative.
    *   Raise `ValueError` with "target_width must be >= 0" if `target_width` is negative.

*   Update `PadImages` class in `keras/src/ops/image.py`:
    *   Perform the same validations as `pad_images` during `compute_output_spec`.
    *   Raise `ValueError` with "target_width must be >= 0" if `target_width` is negative.

*   Update `crop_images` function in `keras/src/ops/image.py`:
    *   Ensure input image tensor rank is 3 or 4; raise `ValueError` otherwise.
    *   Validate that exactly two of `top_cropping`, `bottom_cropping`, and `target_height` are non-None; raise `ValueError` with "Must specify exactly two of top_cropping, bottom_cropping, target_height" if not.
    *   Validate that exactly two of `left_cropping`, `right_cropping`, and `target_width` are non-None.
    *   Raise `ValueError` with "top_cropping must be >= 0" if `top_cropping` is negative or derived negative.
    *   Raise `ValueError` with "target_height must be >= 0" if `target_height` is negative.
    *   Raise `ValueError` with "target_width must be >= 0" if `target_width` is negative.

*   Update `CropImages` class in `keras/src/ops/image.py`:
    *   Perform the same validations as `crop_images` during `compute_output_spec`.
    *   Raise `ValueError` with "target_width must be >= 0" if `target_width` is negative.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.