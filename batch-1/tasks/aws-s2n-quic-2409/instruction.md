Implement a worker manager for a TCP stream server that coordinates a fixed pool of connection handlers. Ensure the manager maintains a bounded pool of connection slots, handles preemption based on connection duration, and drives workers asynchronously without unnecessary polling.

Requirements:

*   Define the `Manager` struct:
    *   Make it generic over a `Worker` type.
    *   Construct it from an iterator of pre-allocated worker instances.
    *   Store workers as a fixed-capacity pool.
    *   Expose a public field `inner` with a public field `workers` that is indexable by `usize`.

*   Implement `Manager` methods:
    *   `new(workers: impl IntoIterator<Item = W>) -> Manager<W>`: Create a manager from an iterator of workers.
    *   `capacity(&self) -> usize`: Return the total number of worker slots.
    *   `active_slots(&self) -> usize`: Return the number of currently active slots.
    *   `max_sojourn_time(&self) -> Duration`: Return the maximum allowable connection duration.
    *   `insert<Pub, C>(&mut self, remote_address: SocketAddress, stream: W::Stream, cx: &mut W::Context, connection_context: W::ConnectionContext, publisher: &Pub, clock: &C) -> bool`: Assign a new connection to a slot, returning true if successful.
    *   `poll<Pub, C>(&mut self, cx: &mut W::Context, publisher: &Pub, clock: &C) -> ControlFlow<()>`: Drive workers that have been woken since the last poll.

*   Define the `Worker` trait:
    *   Include associated types: `Context`, `ConnectionContext`, `Stream`.
    *   Implement methods:
        *   `replace<Pub, C>(&mut self, remote_address: SocketAddress, stream: Self::Stream, connection_context: Self::ConnectionContext, publisher: &Pub, clock: &C)`.
        *   `poll<Pub, C>(&mut self, task_cx: &mut task::Context, cx: &mut Self::Context, publisher: &Pub, clock: &C) -> Poll<Result<ControlFlow<()>, Option<io::Error>>>`.
        *   `queue_time(&self) -> Timestamp`.
        *   `is_active(&self) -> bool`.

*   Implement `Entry<W>` struct:
    *   Include fields: `worker: W`, `waker: Waker`.
    *   Ensure `waker` can be used to mark workers for re-polling.

*   Ensure the manager:
    *   Tracks the number of active slots.
    *   Handles preemption when a connection exceeds `max_sojourn_time`.
    *   Polls only workers that signal readiness.
    *   Transitions slots to idle when workers complete or encounter errors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.