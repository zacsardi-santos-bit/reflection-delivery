## Description

Haystack currently lacks a unified generator component for working with HuggingFace's hosted inference services. Developers who want to use models either through HuggingFace's serverless cloud endpoint or through a self-hosted text generation inference server have no single component that handles both cases, validates the required parameters at startup, and supports streaming output.

## Expected Behavior

- A new generator component should accept either a serverless API type (pointing to a model by name) or a self-hosted inference server type (pointing to a URL).
- On initialization, the component should validate that the necessary connection details are provided: a model name for the serverless API, or a well-formed HTTP/HTTPS URL for the self-hosted server.
- If the model does not exist, initialization should fail with an appropriate error.
- If the URL is not a valid HTTP/HTTPS address, initialization should fail with a clear error.
- The component should support a default token limit of 512 new tokens, with stop words configurable at construction time.
- When running, it should return a list of generated text replies along with associated metadata.
- It should support streaming: when a streaming callback is provided, the component should invoke it for each generated token chunk as it arrives.
- The component should be fully serializable (saveable and restorable as part of a pipeline).

## Why This Matters

Teams building production pipelines need reliable integrations with HuggingFace's inference infrastructure. Without proper parameter validation and a consistent interface, developers can encounter silent misconfiguration or runtime failures. A well-validated, serializable component with streaming support reduces integration friction and makes pipelines more robust.

## Additional Utility

A standalone HTTP/HTTPS URL validation helper is also needed, which correctly identifies valid web URLs while rejecting other schemes, bare hostnames, incomplete URLs, and empty strings.
