## Description

The Go SDK's harness module manages bidirectional state communication between pipeline workers and the runner during execution. The current state channel implementation does not properly handle errors that occur on either the read side (receiving responses) or the write side (sending requests). When such an error occurs, callers waiting for a response can be left blocked indefinitely, the execution context is never cancelled, and there is no mechanism to signal that the connection needs to be torn down and recreated.

## Expected Behavior

- When the state channel encounters a read error (including end-of-stream), any pending callers should receive a meaningful error instead of blocking forever.
- When a write error occurs, the same propagation should happen: callers unblocked with an error, context cancelled, and a recovery callback triggered.
- After the channel has failed, any new requests submitted to it should immediately return the same error rather than blocking.
- A configurable callback should be invoked when the channel terminates due to an error, allowing the channel manager to clean up and recreate the connection.
- The execution context tied to the channel should be cancelled when an unrecoverable error occurs, allowing dependent work to clean up promptly.

## Why This Matters

Without proper error propagation, a transient network failure or a remote end closing the connection can silently stall the entire pipeline worker, because state reads block waiting for responses that will never arrive. With these fixes, errors surface quickly, the worker can attempt reconnection, and pipelines degrade gracefully rather than hanging.
