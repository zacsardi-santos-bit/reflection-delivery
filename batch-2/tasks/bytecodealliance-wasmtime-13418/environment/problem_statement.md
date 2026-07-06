## Description

Wasmtime's async component model currently lacks support for passing streams and futures across component instance boundaries within the same store. If one component instance creates a stream or future through an async function, those resources cannot be transferred to and consumed by a different component instance. This is a significant gap for multi-component architectures.

## Expected Behavior

- A component that exposes an async function returning a stream and a future should be able to produce those resources.
- The stream and future produced by one component instance should be passable to a separate component instance for consumption.
- Both the stream (containing a sequence of bytes) and the future (resolving to a single byte value) should be successfully read by the receiving component instance.
- The cross-instance transfer should work correctly when both operations run concurrently.

## Why This Matters

Multi-component systems often need to pipeline async data: one component produces data streams or future values that another component processes. Without cross-instance stream and future passing, such designs are impossible. This feature enables component-based architectures where data producers and consumers are separate component instances within the same Wasmtime store.
