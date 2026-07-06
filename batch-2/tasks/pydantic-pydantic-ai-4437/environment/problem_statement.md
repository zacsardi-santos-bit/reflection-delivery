## Description

When using the Google AI model in streaming mode, errors that occur partway through a stream — after some content has already been delivered — are not wrapped into the framework's standard error types. Instead, they bubble up as raw, low-level SDK exceptions, which breaks the consistent error-handling contract that developers rely on.

For non-streaming requests, errors are already properly translated into the framework's error hierarchy (distinguishing HTTP errors with status codes from general API errors). But during streaming, if the remote service starts sending a response and then encounters an issue (such as a rate limit or service outage), the raw SDK exception leaks through rather than being caught and re-raised as a typed framework error.

## Expected Behavior

- If a rate-limit or service-unavailability error occurs mid-stream, developers should receive a structured HTTP error with the correct status code and the original error details preserved in the body.
- If a non-HTTP API error (e.g., a redirect) occurs mid-stream, developers should receive a generic model API error with the model name attached.
- The error type and structure should be the same regardless of whether the error occurred before or during streaming.

## Why This Matters

Developers building retry logic, error logging, or user-facing error messages depend on being able to catch a predictable set of error types. When raw SDK errors escape from streaming calls, this error-handling code fails silently or crashes, making it much harder to build reliable applications on top of streaming AI responses.
