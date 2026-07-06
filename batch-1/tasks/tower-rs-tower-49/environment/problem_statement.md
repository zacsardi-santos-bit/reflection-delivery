## Description

When building resilient service pipelines, it's important to be able to cap the number of requests that are concurrently in-flight to a downstream service. Without such a cap, a slow or overwhelmed downstream can accumulate unbounded concurrent requests, leading to resource exhaustion.

We need a middleware layer that wraps any service and enforces a configurable maximum number of simultaneously in-flight requests. Once that limit is reached, the service should signal that it is not ready to accept more requests. Any attempt to send a request when at capacity (without waiting for readiness) should fail immediately.

## Expected Behavior

- The middleware is configured with a maximum concurrent request count at construction time.
- When at capacity, the service reports not-ready, blocking further requests until a slot is freed.
- A slot is freed when a response completes (successfully or with an error).
- A slot is freed when the response future is dropped before it resolves.
- A slot reserved via a readiness check is freed when the service instance holding that reservation is dropped before sending a request.
- Multiple clones of the middleware share the same capacity pool.

## Why This Matters

This middleware is foundational for building backpressure-aware service stacks. It ensures that a downstream service is never asked to handle more concurrent load than it can safely process, and that capacity accounting remains accurate across all lifecycle events (completion, error, and cancellation).
