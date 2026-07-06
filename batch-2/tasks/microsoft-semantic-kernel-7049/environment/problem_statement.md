## Add Mistral AI Chat Completion Connector

## Description

The Semantic Kernel Python SDK currently supports several AI providers for chat completion (OpenAI, Azure OpenAI, etc.) but has no built-in support for Mistral AI models. Developers who want to use Mistral AI through Semantic Kernel must build custom integrations themselves, without the consistency, error handling, and configuration patterns the framework provides for other services.

We need a native Mistral AI connector that integrates with the existing chat completion abstraction so that Mistral AI models can be used interchangeably with other supported providers.

## Expected Behavior

- A new chat completion service for Mistral AI that plugs into the existing provider abstraction
- The service should be configurable via constructor arguments or dedicated environment variables for the model identifier and API key
- Initialization should fail with a clear error when required configuration (API key or model ID) is missing
- The service should support both standard (non-streaming) and streaming chat message retrieval
- Errors from the underlying Mistral AI client should be translated into consistent framework-level exceptions
- A dedicated prompt execution settings class should be provided, supporting common generation parameters (temperature, top-p sampling, max tokens, and messages)
- Attempting to use function-choice behavior (not yet supported by this connector) should raise a clear "not implemented" error

## Why This Matters

Adding this connector enables developers to swap in Mistral AI models with the same code patterns they already use for other providers, without needing to write custom plumbing or error-handling logic.
