I'm hitting a weird hang with the gRPC-Gateway. When an HTTP client cancels a request to a POST endpoint that has no request body binding defined in the HTTP annotation, the server-side gRPC handler context never gets cancelled, even after the client disconnects. My handler is sitting there waiting on context cancellation to do its cleanup and it just hangs forever.

It's specifically the no-body POST case. Regular body-mapped POST endpoints and GET endpoints are fine, so it's isolated to POST routes that don't specify a request body mapping. And it happens for both unary RPCs and server-streaming RPCs routed through the gateway, so whatever the fix is needs to cover both variants.

The root cause as far as I can tell is that the gateway isn't consuming the HTTP request body for these no-body routes. Since the body is never read or discarded, the underlying HTTP transport can't detect that the client has gone away, so the cancellation never propagates down to the handler context. So the fix is basically to drain/consume that body so the transport notices the disconnect and cancels promptly.

What I want: when a client disconnects from one of these no-body POST endpoints, the server-side handler context (or stream context for the streaming case) should be cancelled promptly so handlers relying on cancellation for cleanup, aborting long-running work, or respecting client timeouts actually work instead of running to completion and wasting resources. This matters because a lot of handler logic depends on that signal.

Oh and it's not enough to just patch the runtime, the gateway code generator itself needs updating so all future generated code following this same no-body POST pattern handles it correctly too.
