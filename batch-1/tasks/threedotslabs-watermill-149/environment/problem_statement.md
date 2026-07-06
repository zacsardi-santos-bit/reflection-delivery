## Description

Right now, middleware added to a router applies to every handler registered with that router. There is no way to attach middleware to only one specific handler without spinning up a separate router. This makes it unnecessarily cumbersome to apply cross-cutting concerns — like special error handling, authorization, or tracing — to a subset of handlers.

## Expected Behavior

- When middleware is added directly to the router, it continues to apply to all handlers (existing behavior preserved).
- When adding a handler to the router, the return value should allow the caller to attach additional middleware that applies **only** to that handler.
- Handler-specific middleware must not affect any other handlers registered with the same router.
- The overall ordering rule must remain consistent: all middleware (whether scoped to the whole router or a single handler) is applied in the reverse of the order it was registered, so the last-registered piece of middleware runs outermost.

## Why This Matters

Without per-handler middleware, teams are forced to either create multiple routers or add conditional logic inside shared middleware to filter by handler. Per-handler middleware scoping makes routers more ergonomic and reduces unnecessary coupling between handlers.
