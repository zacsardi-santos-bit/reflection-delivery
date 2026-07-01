Implement functionality to ensure that when an HTTP client disconnects from a no-body POST endpoint, the server-side gRPC handler context is promptly cancelled. Update the gateway code generator to handle this correctly for future generated code.

*   Export the function `NoBodyPostServer_RetrieveContextRPC()` in the server package.
    *   Ensure it blocks until the unary RPC handler for the no-body POST endpoint starts processing a request.
    *   Return the handler's `context.Context`.

*   Export the function `NoBodyPostServer_RetrieveContextStream()` in the server package.
    *   Ensure it blocks until the server-streaming RPC handler for the no-body POST endpoint starts processing a request.
    *   Return the handler's `context.Context`.

*   Register a unary RPC endpoint at `POST /rpc/no-body/rpc` on the integration test server (port 8088).
    *   Ensure the endpoint has no body annotation in its HTTP mapping.

*   Register a server-streaming RPC endpoint at `POST /rpc/no-body/stream` on the integration test server (port 8088).
    *   Ensure the endpoint has no body annotation in its HTTP mapping.

*   Ensure that when an HTTP client cancels its request context after a handler begins processing a no-body POST unary RPC request, the server-side handler context becomes done (cancelled) within one second.

*   Ensure that when an HTTP client cancels its request context after a handler begins processing a no-body POST server-streaming RPC request, the server-side stream context becomes done (cancelled) within one second.

*   For generated gateway code handling routes with no body annotation:
    *   Drain (discard) the HTTP request body immediately before processing path parameters or query parameters.
    *   Ensure client disconnections propagate correctly as context cancellations to the gRPC handler.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.