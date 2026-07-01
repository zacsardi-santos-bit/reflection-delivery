I'm working on the history replication service in a distributed workflow system, and I need to add the infrastructure for a new streaming-based approach to cross-cluster replication.

There are three things I need implemented in the replication package:

First, a generic bidirectional streaming wrapper that manages the lifecycle of a streaming connection. It should lazily initialize the connection from a provider the first time it's used, be safe to initialize multiple times (idempotent after the first call), and automatically transition to a closed state whenever a send or receive error occurs. Received messages should be delivered through a Go channel that gets closed when the upstream connection ends or errors. After the stream is explicitly closed, attempting to re-initialize it should return an error.

Second, a task progress tracker that keeps an ordered queue of in-flight replication tasks and computes the current low-watermark position. Adding tasks should be idempotent — if a task is already in the queue, it should not be added again. The low-watermark calculation should advance past tasks that have been acknowledged, return a position anchored to the first unfinished task (using that task's ID and creation time) when tasks are still pending or cancelled, and return nil when the queue is empty.

Third, a stream receiver that ties these together: it should read incoming replication messages, wrap each one into an executable task, submit it to a task scheduler, and periodically send acknowledgments back to the source carrying the current watermark position and timestamp. If no watermark is available yet, no acknowledgment should be sent. If the stream returns an error, it should be propagated.

These components need to live in the replication package and work together to support efficient, progress-tracked replication over persistent streams.
