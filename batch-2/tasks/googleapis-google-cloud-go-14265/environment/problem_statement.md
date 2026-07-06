## Description

The HTTP transport layer in this library supports distributed tracing for outgoing API requests, but it currently has no structured logging capability for error responses. When a request fails — due to a rate limit violation, a permission denial, a context timeout, a cancellation, or a network-level error — developers have no automatic way to capture a structured diagnostic record with the relevant error context at the transport layer.

## Expected Behavior

- When an error occurs during an HTTP round trip (whether an error HTTP response or a transport-level failure), the transport should emit a single, structured debug log record containing key diagnostic fields: the error category, the HTTP method, status codes, the affected domain, quota metadata, request resend count, and resource name.
- Errors from structured server responses should populate the log record's error type from the recognized machine-readable reason in the response body. Transport-level context errors (timeout or cancellation) should produce well-known error type labels. Other errors should use a type name derived from the error itself.
- The log record should be deferred until the response body is closed, so that active distributed tracing spans are still open and can be correlated with the log at the time of emission.
- When a response body is too large to buffer (above the 8 KB limit), the transport should log immediately using the HTTP status string, without wrapping the body in the deferred buffering mechanism.
- Logging and tracing must be independently controllable via experimental feature flags, each working correctly with or without the other.
- When logging is disabled, the transport must complete with no logging side effects at all.

## Why This Matters

Without this capability, developers troubleshooting failed API calls have to rely solely on traces, which may not carry the full structured error detail from the server response or distinguish between timeout, cancellation, and server-side error categories. Having structured log records automatically emitted at the transport layer, with span correlation, makes diagnosing production API failures significantly faster.
