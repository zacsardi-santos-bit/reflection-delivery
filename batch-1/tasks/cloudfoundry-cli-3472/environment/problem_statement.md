## Description

The CLI currently makes HTTP requests to Cloud Controller, UAA, and Routing APIs without attaching distributed tracing headers. This makes it difficult or impossible to correlate a single CLI invocation's activity across multiple backend services when investigating failures or performance issues.

## Expected Behavior

- All outgoing HTTP requests from the CLI to Cloud Controller, UAA, and Routing APIs should carry B3 distributed tracing headers — one for the trace ID and one for the span ID.
- A trace ID should be initialized once per CLI invocation (either generated or provided externally) and attached to all requests in that session.
- A span ID should be randomly generated and attached per request.
- If either tracing header is already present on a request (e.g., set explicitly by the caller), it must not be overwritten.
- The trace ID generator should produce 32-character hex strings (suitable for B3 trace IDs), and there should also be a generator for arbitrary-length random hex strings.

## Why This Matters

Without distributed tracing headers, operators cannot trace a CLI command's requests through backend systems. Adding these headers allows platform operators and developers to correlate CLI-initiated requests with server-side traces, dramatically improving observability and debuggability.
