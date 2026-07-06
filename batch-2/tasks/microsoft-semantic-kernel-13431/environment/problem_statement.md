# Handle Image Content in AI Function Tool Results

## Description

When a kernel function returns an image as its result, there is no consistent, correct handling across the different AI backends in Semantic Kernel. Currently, the framework either crashes or produces incorrect behavior when it tries to pass image data back to a model that does not support multimodal content in tool results.

Two different cases need to be addressed:

1. **Backends that do not support multimodal tool results** (such as OpenAI chat completion, the Assistants API, and the Responses API): Instead of serializing the image or throwing an unhandled exception, these backends should substitute a clear, standardized error message in the tool result, informing the model that image content is not supported.

2. **Backends that do support multimodal tool results** (such as Gemini): When an image with binary data is returned by a function, the backend should properly encode the raw bytes inline and transmit them as part of the tool response. If the image data is incomplete (missing binary bytes, or missing a MIME type), a descriptive exception should be raised rather than silently sending malformed data.

## Expected Behavior

- When a function returns an image and the AI backend does not support it in tool results, the tool response sent to the model should contain a human-readable error message rather than crashing.
- The same error message should be used consistently across all non-supporting backends.
- When the backend supports multimodal tool results and a function returns an image with proper binary data and a MIME type, the image should be encoded and transmitted correctly.
- Attempting to send an image that has only a URL reference (no binary data) to a supporting backend should raise a clear error.
- Attempting to send an image with binary data but no MIME type should also raise a clear error.

## Why This Matters

Developers building applications where AI functions capture or generate images currently have no reliable way to include those results in an AI conversation loop. This change ensures the framework handles these cases gracefully and consistently, regardless of which AI provider is in use.
