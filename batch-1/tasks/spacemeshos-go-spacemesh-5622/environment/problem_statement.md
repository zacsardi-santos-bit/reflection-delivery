## Description

The current peer-to-peer request layer uses a callback-based design: to send a request to another node, the caller must provide separate success and error handler functions that are invoked asynchronously when the response arrives. This makes code harder to read, error propagation harder to trace, and forces callers to manage concurrency explicitly. The higher-level data fetching operations (fetching malicious node identities, layer data, and layer opinions from peers) currently fan out to multiple peers in a single call using the same callback pattern, which means errors from downstream operations like fetching proofs or ballots are often silently ignored rather than properly surfaced.

## Expected Behavior

- The network request method should be simplified to a direct synchronous call that returns the response data and any error without requiring callback functions.
- Per-peer data fetching functions (fetching malicious IDs, layer data, layer opinions) should each operate on a single peer and return results directly.
- Errors from downstream operations (such as fetching malfeasance proofs or ballots) should be properly propagated rather than swallowed.
- When processing activation transactions, detecting duplicate malicious submissions should return a proof object directly from the processing function rather than side-effecting only.
- The decision to broadcast a malicious activity proof (during normal gossip operation) versus just storing it (during sync) should be handled at the caller level, not buried in the processor.

## Why This Matters

The callback pattern makes it very difficult to write straightforward, linear error-handling code in Go. Collapsing the fan-out into per-peer synchronous calls lets callers use standard error handling idioms and makes the flow of data and errors obvious. Properly propagating downstream errors also prevents silent failures during synchronization.
