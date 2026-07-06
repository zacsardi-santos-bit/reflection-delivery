## Description

The vLLM server's chat completion endpoint rejects valid requests that contain large payloads. This happens because the web framework's built-in default request body size limit is applied without override, causing any request body larger than approximately 2 MiB to be rejected with an HTTP error.

This is a problem in practice when users pass large template arguments (e.g., lengthy system prompts or template customizations) as part of a chat completion request. Even though the payload is within a reasonable range for real workloads, the server rejects it at the HTTP layer before the request ever reaches the model backend.

## Expected Behavior

- The server should accept chat completion request bodies that are larger than the framework's built-in default limit.
- Specifically, the server should support request bodies up to at least 32 MiB.
- Requests with large template arguments or other large fields should succeed normally and return a valid response.

## Why This Matters

Users sending requests with large template customizations or extended context payloads are hitting silent HTTP-level rejections. Raising the configured body size limit ensures these requests are processed correctly rather than failing with an unintuitive error response.
