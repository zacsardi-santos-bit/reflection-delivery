## Description

We need a way to run NVIDIA inference model servers as sidecar containers alongside Flyte task pods. Right now there is no built-in support for configuring an inference server within the Flyte workflow — developers have to manage this infrastructure separately, which is error-prone and doesn't integrate with Flyte's resource and secrets management.

## Expected Behavior

- A developer should be able to declare an inference server configuration that specifies the model image, memory allocation, port, and authentication credentials.
- The configuration should automatically wire the necessary NGC authentication into the pod (both image pull secrets and API key environment variables).
- Resource settings (memory, CPU, GPU, shared memory) should have sensible defaults so users don't need to specify everything.
- The base URL for connecting to the inference server should default to localhost on the configured port.
- When a developer wants to use LoRA fine-tuned adapters, they should be able to specify one or more Hugging Face repository IDs along with a memory allocation for the download step.
- The system should validate that when LoRA adapters are requested, both the memory allocation and the required environment variable are provided. Clear, actionable error messages should be raised when either is missing.

## Why This Matters

This makes it easy to co-locate a model inference server with a Flyte task without requiring users to manually configure Kubernetes pod specs, secrets injection, or init containers. It also ensures that common mistakes (like forgetting to set required LoRA configuration) are caught early with helpful error messages.
