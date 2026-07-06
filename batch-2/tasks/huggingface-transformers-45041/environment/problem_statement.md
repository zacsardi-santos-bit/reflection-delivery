## Description

Many vision models designed for video or multi-frame inputs use three-dimensional convolutional layers to project image patches into embedding space. At inference time, this convolutional operation is mathematically equivalent to a simple linear (fully-connected) transformation. However, there is currently no standard mechanism in the library to load such a model with the convolutional patch embedding automatically replaced by its faster linear equivalent, while still reading from the original checkpoint format.

## Expected Behavior

- Users should be able to opt into a "patch embedding fusion" when loading a pretrained model. The library should transparently replace compatible convolutional patch embedding layers with linear equivalents, converting the checkpoint weights automatically.
- If the fused configuration is saved alongside the model, subsequent loads should automatically re-apply the fusion without requiring any explicit argument.
- If the model does not contain any compatible modules (e.g., the convolution's stride does not match its kernel size), the system should skip fusion silently without raising an error.
- If a conflicting weight transformation is already registered for the model type, the system should raise a descriptive error rather than silently overwriting it.
- Weight converters that transform between the convolutional and linear weight layouts must support bidirectional conversion so that checkpoints can be saved back in the original format.

## Why This Matters

This enables users to work with a more efficient runtime representation of these models without requiring changes to original checkpoints, model definitions, or inference code. The fused form is particularly useful for deployment scenarios where compute efficiency matters.
