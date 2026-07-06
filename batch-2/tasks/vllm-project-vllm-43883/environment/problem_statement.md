## Description

The vllm Rust server does not currently support attaching a request tracking identifier to HTTP responses, even though the Python-based vllm server already supports this capability. Many API clients, load balancers, and observability tools rely on a standard response header to associate outgoing requests with the responses they receive. Without this, users of the Rust server have no built-in way to track a specific request through the system.

## Expected Behavior

- An opt-in flag should be available so that when enabled, the server attaches a tracking identifier to every HTTP response.
- If a client includes a tracking identifier on the incoming request, the server should echo that same value on the response.
- If the client provides no tracking identifier, the server should generate a fresh unique identifier and include it in the response.
- By default (when the feature is not explicitly enabled), no tracking identifier header should be present on responses.
- The opt-in flag should be expressible both as a command-line argument to the serve subcommand and as a field in the JSON configuration passed to the frontend subcommand.

## Why This Matters

Clients, API gateways, and distributed tracing systems commonly rely on per-request identifiers to correlate logs, metrics, and traces across services. Bringing the Rust server to parity with the Python server on this point ensures users can adopt it without losing observability.
