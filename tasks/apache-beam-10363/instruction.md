Implement proper error handling and lifecycle management for the state channel component in the Go SDK harness for Apache Beam. Ensure that errors encountered during state communication are propagated to waiting callers, cancel the execution context, and trigger a configurable callback for channel recreation.

*   Update the `makeStateChannel` function to:
    *   Accept a context, a context cancel function, a string ID, and a client interface.
    *   Return a `*StateChannel` with background read and write goroutines running.
*   Modify the `StateChannel` struct to include:
    *   An unexported field `mu` of type `sync.Mutex`.
    *   An unexported field `responses` of type `map[string]chan<- *pb.StateResponse`.
    *   An unexported field `forceRecreate` of type `func(string, error)`.
*   Enhance `StateChannel.Send` to:
    *   Assign a unique ID to each outgoing request and register a response channel in the `responses` map.
    *   Ensure correct response routing for concurrent `Send` calls.
    *   Handle `io.EOF` from `Recv` by returning an error, cancelling the context, and invoking `forceRecreate`.
    *   Handle non-EOF errors from `Recv` by returning an error, cancelling the context, and invoking `forceRecreate`.
    *   Handle `io.EOF` from `Send` by returning an error, cancelling the context, and invoking `forceRecreate` with the actual error.
    *   Handle non-EOF errors from `Send` by returning an error, cancelling the context, and invoking `forceRecreate`.
    *   Return an error for subsequent `Send` calls after an unrecoverable error without blocking.
    *   Unblock `Send` calls when a response channel is removed from the `responses` map before dispatching a response.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.