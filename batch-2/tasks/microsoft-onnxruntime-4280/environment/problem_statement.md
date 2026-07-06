## Description

The ONNX Runtime training module needs a structured way for users to configure optimizers (SGD, Adam, and Lamb) when setting up a training pipeline. Currently, there is no dedicated configuration object that captures optimizer hyperparameters with proper defaults and input validation. This makes it difficult for developers to express optimizer settings in a clean, validated way before passing them to the trainer.

## Expected Behavior

- There should be dedicated configuration objects for each supported optimizer type (SGD, Adam, and Lamb) that carry hyperparameters with sensible defaults.
- SGD configuration should default to a learning rate of 0.001 and should not support per-parameter hyperparameter groups — attempting to provide them should result in a clear error.
- Adam configuration should default to: learning rate 0.001, alpha 0.9, beta 0.999, lambda coefficient 0.0, epsilon 1e-8, bias correction enabled, and a specific weight decay mode. It should expose a weight decay mode enum with at least a "before weight update" variant.
- Lamb configuration should default to: learning rate 0.001, alpha 0.9, beta 0.999, lambda coefficient 0.0, epsilon 1e-6, ratio bounds of negative infinity and positive infinity, and bias correction enabled.
- Adam and Lamb configurations should support optional per-parameter hyperparameter groups, where per-parameter values take precedence over global defaults.
- Learning rate must not be overridable at the per-parameter level for Adam and Lamb — attempting to do so should raise a clear error.
- Invalid inputs (wrong types, negative learning rates, unknown optimizer names, missing required keys, mismatched hyperparameter keys between defaults and parameter groups) should all be rejected with an error.

## Why This Matters

Without these configuration objects, users have no consistent, validated way to express optimizer settings. The new configuration classes provide a clean API with early validation, reducing the chance of misconfigured training runs.
