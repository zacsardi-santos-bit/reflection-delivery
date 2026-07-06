## Description

Keras models trained using the PyTorch backend cannot currently be exported to PyTorch's native portable program format. This makes it difficult to deploy Keras-trained models in production environments where only PyTorch is available — without requiring Keras at inference time.

We need a new export format option that serializes a Keras model into a self-contained PyTorch portable program file. The exported file should be loadable using PyTorch's own loading utilities and runnable without any Keras dependency.

## Expected Behavior

- The model's export method should accept a new PyTorch format option that saves the model as a portable program file with the required file extension.
- The exported artifact, when loaded and run, must produce outputs numerically identical to those of the original Keras model.
- The export must support a wide range of model architectures: sequential, functional (including skip connections), subclassed models, models with multiple inputs (both list and dict), and models with multiple outputs.
- An optional input shape specification should be accepted, as well as options to control strict tracing, dynamic batch dimension support, and other export behaviors.
- Passing unsupported options should raise a clear error message indicating which arguments are not supported.
- If the specified filepath does not use the required portable program file extension, an error should be raised.
- Models that cannot be statically traced (e.g., those with data-dependent branching) should raise a runtime error when strict export mode is requested.

## Why This Matters

PyTorch users who want to leverage Keras for model building should be able to deploy those models natively in PyTorch pipelines without carrying a Keras dependency at inference time. This is a common requirement in production environments where binary size, startup time, and dependency management matter.
