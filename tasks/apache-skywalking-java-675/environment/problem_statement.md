## Description

The SOFA-RPC plugin currently supports tracing for synchronous RPC calls, but when using callback-style (asynchronous) invocation, the distributed trace context is not carried into the callback thread. This means that any work done in the response callback is invisible in the trace — it appears as a disconnected, untraceable operation rather than a continuation of the original call chain.

## Expected Behavior

- When an asynchronous RPC call is made using callback mode, the trace context from the initiating thread should be propagated into the callback thread that handles the response.
- The callback processing should appear as a linked segment in the distributed trace, connected to the span that made the original RPC call.
- If the callback encounters an error or exception, that error should be recorded in the trace as a log entry on the callback's span.
- If no trace is active at the time the callback is registered, the callback thread should still complete normally without errors.

## Why This Matters

Without this support, operators and developers cannot observe the full end-to-end lifecycle of a callback-based RPC call. The asynchronous response handling phase is simply missing from traces, making it difficult to debug latency issues or failures that occur during callback execution. Adding cross-thread trace propagation for callback invocations makes the distributed trace complete and accurate for this invocation style.
