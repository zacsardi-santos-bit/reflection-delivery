## Description

Two capabilities are missing from the neural network model framework that are needed for proper uncertainty estimation and model inspection.

**Predicting intermediate layer outputs**: Right now, when you call the prediction method on a model, you only get back the model's final declared outputs. There's no way to ask for the output of an arbitrary internal layer or tensor. This makes it difficult to inspect learned representations, debug models, or use intermediate computations in downstream tasks.

**Controllable dropout for uncertainty estimation**: The standard dropout mechanism automatically disables itself during inference. This is a problem for Monte Carlo dropout-based uncertainty estimation, which requires dropout to be active at prediction time. Currently there is no layer that supports being explicitly switched on or off at inference time, forcing users to build workarounds or separate model architectures.

## Expected Behavior

- The prediction API should accept an optional parameter specifying which internal tensors to return output values for, instead of always returning the model's declared outputs.
- There should be a dropout layer that takes an explicit on/off switch as an input, allowing the caller to control whether dropout is applied regardless of whether the model is in training or inference mode.
- The data generator interface should support a mode string (rather than a boolean flag) to distinguish between training, prediction, and uncertainty estimation scenarios, so subclasses can yield appropriately structured inputs for each mode.

## Why This Matters

These changes together enable building proper uncertainty-aware models in a single architecture: one model that applies dropout during uncertainty estimation while making clean deterministic predictions during normal inference.
