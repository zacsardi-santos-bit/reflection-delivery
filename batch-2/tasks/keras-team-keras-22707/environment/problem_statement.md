## Description

The image padding and cropping operations lack sufficient input validation, leading to confusing or unhelpful errors when users pass invalid arguments. Currently, passing negative values for padding amounts, cropping amounts, or target dimensions — or providing image inputs with the wrong number of dimensions — does not produce clear, early error messages. Users see low-level failures or unexpected behavior instead of being told precisely what was wrong with their input.

## Expected Behavior

- When an image with an incorrect number of dimensions (not 3 or 4) is passed to the padding or cropping operation, a clear error should be raised immediately.
- When the combination of padding/cropping parameters is invalid (e.g., all three of the top offset, bottom offset, and target dimension are all specified at once), the error message should clearly state that exactly two of those three values must be provided.
- When any padding or cropping offset parameter is negative, the operation should raise an informative error naming the specific parameter and indicating it must be non-negative.
- When a target height or target width dimension is negative, the operation should raise an informative error naming the specific parameter.
- These validations should apply consistently both when working with symbolic computation graphs (e.g., during model building) and during eager execution with real image data.

## Why This Matters

Without these validations, users debugging image preprocessing pipelines have to trace through internal logic to understand why padding or cropping failed. With clear, early validation and descriptive error messages, users can immediately identify and correct the problematic argument.
