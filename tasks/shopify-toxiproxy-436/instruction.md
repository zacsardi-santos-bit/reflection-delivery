Implement a timeout-based mechanism for removing toxics from active connections in Toxiproxy to prevent deadlocks. Ensure the system handles blocked output channels gracefully by using a timeout for writes and returning descriptive errors when necessary.

*   Implement the `WriteOutput` method in the `ToxicStub` type located in `toxics/toxic.go`.
    *   Accept parameters: `p *stream.StreamChunk`, `d time.Duration`.
    *   Return an error if the write operation times out.
    *   If `d` is non-zero and no consumer reads from the output channel within the duration, return an error with the message: "timeout: could not write to output in %d seconds", where `%d` is the integer number of seconds of the timeout.
    *   If a consumer reads from the output channel before the timeout expires, send the `StreamChunk` to the output channel and return `nil`.
    *   If `d` is zero, block until a consumer reads from the output channel and then return `nil`.
    *   Ensure the data sent to the output channel is identical to the `StreamChunk` passed as input.

*   Implement the `NewToxicStub` function in `toxics/toxic.go`.
    *   Accept input and output channels of type `chan *stream.StreamChunk`.
    *   Return a pointer to a new `ToxicStub` that is ready to use with `WriteOutput`.

*   Ensure that removing a toxic from an active connection completes without deadlocking or panicking, even when the output channel is temporarily blocked.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.