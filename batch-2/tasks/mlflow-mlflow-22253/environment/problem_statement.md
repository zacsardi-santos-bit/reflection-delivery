## Description

MLflow currently has no native support for diffusion model adapters (such as fine-tuned adapter weights). Data scientists who fine-tune diffusion models with adapter techniques are unable to use MLflow's standard model tracking, versioning, and serving workflows to manage their adapter weights alongside the reference to the base model they were trained on.

## Expected Behavior

- There should be a dedicated MLflow flavor for saving and loading diffusion model adapters, accessible via the standard MLflow namespace
- Saving a model should accept a path to adapter weights (either a single file or a directory), a reference to the base diffusion model, and an optional adapter technique specifier (with a sensible default), and should produce a complete, loadable MLflow model artifact
- The saved artifact should include all standard MLflow model files (environment specifications, model signature, metadata), and the flavor configuration should record the base model identifier, adapter type, and the resolved version of the base model from the model hub when available
- When loading a saved model, the returned object should expose the base model reference, adapter type, adapter weight location, and the resolved base model version
- The loaded model should support on-demand pipeline loading with an optional base model override, and raise a clear error if the base model cannot be found
- The model should also be loadable via the standard model serving interface, accepting text prompts (as strings, lists, dicts, or DataFrames) and returning the generated images as binary data
- Prediction must validate its inputs and raise informative errors for missing prompt fields, empty inputs, wrong types, and pipeline failures
- The default pip requirements for the flavor should include the required core machine learning and model serving libraries, plus optional packages when they are already installed

## Why This Matters

Without a native flavor, teams cannot use MLflow to version and reproduce their adapter-based diffusion model experiments, serve fine-tuned models in a standardized way, or share adapter artifacts with the same governance tooling they use for all their other models. Adding this flavor brings diffusion model adapters into the MLflow ecosystem as first-class citizens.

Additionally, a shared utility for validating HuggingFace repository identifiers should be available in a common location so that it can be reused across multiple MLflow flavors without duplication.
