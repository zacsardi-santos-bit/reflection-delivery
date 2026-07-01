I've noticed that when an HTTP client cancels a request to a POST endpoint that has no body mapping defined in the gRPC-Gateway HTTP annotation, the server-side gRPC handler context is never cancelled — even after the client disconnects. My handler is waiting on context cancellation to clean up, but it just hangs forever. 

This only happens with POST endpoints that don't specify a request body binding. Regular body-mapped POST endpoints and GET endpoints work fine. The problem exists for both unary RPCs and server-streaming RPCs mapped through the gateway.

The fix should ensure that when a client disconnects from one of these no-body POST endpoints, the server-side context is cancelled promptly so that handlers relying on context cancellation work correctly. The gateway code generator should also be updated so this is handled correctly for all future generated code with the same pattern.
