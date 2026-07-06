## Description

The cloud cluster transport layer has a few issues that need to be addressed:

1. A utility function for obtaining a free TCP port lives inside a test file in one package, making it inaccessible to other packages that need it (such as the transport layer tests).
2. The gRPC server component has been renamed from an internal type but the code and its constructor have not been updated to match, causing confusion.
3. The client methods that wrap address resolution and connection dialing do not produce descriptive errors — when resolution or dialing fails, callers cannot tell from the error which step went wrong.
4. The gRPC server component has no tests at all, meaning error handling paths (empty address, invalid address, gRPC status code mappings for specific error conditions) are completely uncovered.

## Expected Behavior

- A shared utility package should expose the free TCP port helper so it can be used across multiple packages.
- The server constructor and type should use consistent, updated naming.
- When address resolution fails in any client operation (join, notify, remove, apply), the returned error must clearly indicate that resolution was the problem.
- When the network connection (dial) fails, the returned error must clearly indicate that dialing was the problem.
- The server must return a clear error when started with an empty address.
- Specific internal error conditions (store not open, leader not found, not the leader) must be mapped to appropriate gRPC status codes so clients receive meaningful, structured errors.
- An address resolver that computes the RPC port from a raft address must handle both a fixed-port mode and an increment-by-one mode, returning structured errors for invalid input.

## Why This Matters

Without these improvements, debugging cluster communication failures is difficult because errors from different failure modes are indistinguishable. Centralizing the TCP utility function also removes code duplication and allows the transport layer to be fully tested.
