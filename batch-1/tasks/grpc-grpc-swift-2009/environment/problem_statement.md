## Description

The gRPC Swift code generator currently produces client protocol types with lower-level RPC methods that require callers to manually construct a request wrapper object before each call. Even for a simple unary request, developers must create the request container, pass it in, and then extract the response value from the response wrapper — three steps for what should be a one-liner. This verbosity makes everyday usage unnecessarily tedious.

## Expected Behavior

- The code generator should produce additional convenience methods alongside the existing low-level protocol methods, allowing callers to pass a message directly without constructing request objects by hand.
- For RPC patterns that receive a single response, there should be a sensible default response handler that simply returns the response value, so callers don't need to provide one.
- For RPC patterns that send a stream of requests, the generated convenience method should accept a producer closure rather than requiring the caller to create a streaming request object.
- Services with no methods should still produce an (empty) convenience extension.
- The generated convenience methods should use the same access level (public, package, or internal) as the rest of the generated code.
- These generated files should be updated wherever the code generator is used throughout the codebase.

## Why This Matters

Reducing boilerplate in generated client code lowers the barrier to making gRPC calls from Swift and aligns with Swift's emphasis on ergonomic, expressive APIs. Developers should be able to make a unary call in a single expression without any intermediate request construction steps.
