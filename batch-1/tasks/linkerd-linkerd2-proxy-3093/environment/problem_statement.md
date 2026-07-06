## Description

The outbound proxy supports per-route timeouts today, but it has no way to automatically retry requests that fail due to backend errors or per-attempt time overruns. When a backend returns an error (for example, a server-side failure status or a gRPC error code), that failure goes straight through to the caller — even if a second attempt would have succeeded.

## Expected Behavior

Route policies should support an optional retry configuration with the following controls:

- **Maximum retry count**: how many additional attempts are made after the first failure.
- **Request body buffer size**: the maximum number of bytes of the request body to buffer so retries can replay it.
- **Per-attempt timeout**: an optional time limit for each individual attempt; if an attempt exceeds this limit it is treated as a retryable failure.
- **Retryable conditions for HTTP routes**: a set of HTTP status code ranges that trigger a retry.
- **Retryable conditions for gRPC routes**: a set of gRPC status codes that trigger a retry.

When a request fails in a retryable way and attempts remain, the proxy must transparently re-issue the request. When all retry attempts are exhausted, the proxy must return the final response (even if it is still an error) to the caller. When a per-attempt timeout is configured and retries are exhausted, the final attempt must not be cut short by the per-attempt deadline.

When a route-level overall request timeout is also configured, it still applies; if the retry budget is exhausted and the overall deadline has been reached, the caller receives a deadline-exceeded error.

## Why This Matters

Without automatic retries at the proxy layer, every transient backend error — a pod restarting, a brief overload, or a spurious failure — is visible to the calling service. Adding retry support at the routing layer lets the proxy absorb short-lived failures transparently, improving reliability without requiring application-level changes.
