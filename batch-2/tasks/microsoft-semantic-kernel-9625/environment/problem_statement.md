## Description

The project provides AI connector services for converting audio to text and generating images from text, but there is no equivalent service for converting text into audio output. Developers who want to build applications with speech synthesis capabilities — such as having a chatbot respond verbally — have no way to do this through the existing connector infrastructure.

## Expected Behavior

- A new text-to-audio service should be available for both standard and Azure-hosted deployments, following the same conventions as existing media services
- The service should read its model/deployment configuration from environment variables, with the ability to override through constructor parameters
- Calling the service with a text string should return audio content that includes the binary audio data and the model identifier
- The service should support serialization and deserialization through dictionary settings
- Appropriate errors should be raised when required configuration (model ID, API key, endpoint) is missing or invalid
- The existing audio-to-text and text-to-image services should expose their execution settings classes through a consistent accessor method
- Relevant classes should be accessible from the top-level package namespace, consistent with how other services are exposed

## Why This Matters

Without this capability, developers must use separate, unintegrated approaches to add speech synthesis to their Semantic Kernel applications. Adding a first-class text-to-audio service brings the audio output scenario in line with existing input and image generation services, enabling full audio-capable conversational applications.
