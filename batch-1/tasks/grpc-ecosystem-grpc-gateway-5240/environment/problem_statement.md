## Description

When a gRPC service method is mapped to an HTTP POST endpoint without a request body binding, the gateway does not properly propagate client disconnections to the server-side handler context. As a result, if an HTTP client cancels its request while the server is processing it, the gRPC handler's context is never cancelled — even though the client is gone. Any handler logic that depends on context cancellation for cleanup or early exit will hang indefinitely.

## Expected Behavior

- When an HTTP client disconnects from a no-body POST endpoint (unary RPC), the server-side handler context should be cancelled promptly.
- When an HTTP client disconnects from a no-body POST endpoint (server-streaming RPC), the server-side stream context should be cancelled promptly.
- Both unary and streaming variants of this pattern should behave correctly.

## Root Cause

The gateway was not consuming the HTTP request body for routes with no body annotation. Because the body was never read or discarded, the underlying HTTP transport could not detect that the client had disconnected, so the context cancellation was never propagated to the gRPC handler.

## Why This Matters

Many gRPC handler implementations rely on context cancellation for resource cleanup, aborting long-running work, or respecting client timeouts. If the context is never cancelled when a client disconnects, handlers will run to completion unnecessarily — wasting resources and potentially causing incorrect behavior.
