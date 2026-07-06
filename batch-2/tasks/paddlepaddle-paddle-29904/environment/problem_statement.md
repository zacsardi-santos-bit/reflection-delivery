## Description

There are two related issues with saving neural network layers in dynamic-to-static mode:

1. **No validation of input spec compatibility at save time**: When a layer's forward method is decorated to declare static input specifications, and the model is then saved with a *different* set of input specifications, the framework currently performs no compatibility check. This means type mismatches (e.g., saving with a different dtype than what was declared), shape rank mismatches, or concrete shape dimension conflicts are silently accepted even though they will lead to incorrect behavior. The framework should instead raise a clear error when the provided specifications conflict with the declared ones.

2. **Save-load-save workflow fails**: It is currently not possible to load a previously saved model and immediately re-save it with new input specifications, without first running inference on it. This round-trip (save → load → re-save) is a common pattern — for example, after distribution or fine-tuning — but the re-save step currently fails or produces a broken model.

## Expected Behavior

- When providing input specifications at save time that conflict with a layer's declared specifications (dtype mismatch, shape rank mismatch, or concrete shape dimension mismatch), a validation error should be raised immediately.
- Compatible specifications — including using concrete dimension sizes where the declaration used dynamic dimensions — should be accepted without error.
- If no override specifications are provided, the layer's declared specifications should be used as-is.
- A model that has been loaded from disk should be re-savable without running inference first, and the re-saved model should produce numerically equivalent results when reloaded.

## Why This Matters

Without validation, incorrect input specifications can be silently accepted, leading to subtle errors at inference time. The save-load-save workflow is important for model redistribution and fine-tuning pipelines where re-specifying input shapes is needed without re-running forward passes.
