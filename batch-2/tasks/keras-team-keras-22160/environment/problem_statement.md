## Description

Functional models currently allow an absent value to be passed for any input without any validation against whether that input was intended to be required or optional. This makes it possible to silently pass an absent value for a required model input with no clear error, leading to confusing downstream failures.

## Expected Behavior

- When a model input is declared as required (non-optional) and an absent value is passed for it at inference time, the model should immediately raise a clear error indicating that the input is not optional and that a valid value must be provided.
- When a model input is declared as optional and an absent value is passed for it, the model should accept the absent value and compute the correct output.
- This validation should work consistently regardless of whether the model is called with inputs as a list or as a dictionary.
- The validation should also apply during model training — passing an absent value for a required input during a training run should surface the same clear error rather than a generic one.

## Why This Matters

Users who build multi-input models sometimes need to represent inputs that are genuinely optional (e.g., side inputs used only for some examples). The framework should allow them to mark inputs explicitly as optional or required. Required inputs should be guarded so that missing values are caught early with a clear, actionable message, rather than propagating silently into the computation graph.
