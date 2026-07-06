## Description

The Azure OpenAI connector in Semantic Kernel is missing support for text-to-audio (text-to-speech) generation. While Azure OpenAI exposes this capability through its API, there is currently no way for developers using the Azure OpenAI integration to generate spoken audio from text input via the framework's standard service abstractions.

Other modalities (chat completion, text embedding, text-to-image) are already supported by the Azure OpenAI connector. Text-to-audio should be available in the same way — registerable through dependency injection and accessible via the shared audio service interface.

## Expected Behavior

- Developers should be able to register an Azure OpenAI text-to-audio service in their dependency injection container, providing a deployment name, endpoint URL, and API key.
- The registered service should be retrievable via the standard text-to-audio service interface.
- The service should support selecting from a set of available voices and output audio formats.
- Requesting audio with an unsupported voice or format should fail with an informative error rather than silently misbehaving.
- When generating audio, the model to use should be resolved from a priority order: the model configured at construction time takes precedence, followed by any model specified in per-request settings, with the deployment name used as a final fallback.
- If the HTTP client used by the service has a base address configured, that should take precedence over the endpoint provided at construction time.
- The service should expose metadata attributes for the deployment name and model ID.

## Why This Matters

Applications that use Azure OpenAI often need to support audio output alongside text generation. Without this service, developers must implement their own Azure OpenAI audio integration from scratch, bypassing Semantic Kernel's unified service abstractions. Adding this service brings Azure OpenAI feature parity with the existing integration patterns in the framework.
