# Streaming-Based Cross-Cluster Replication Infrastructure

## Description

The history replication subsystem currently relies on a polling-based mechanism for replicating workflow data between clusters. We need to add foundational infrastructure for a new streaming-based approach that uses persistent bidirectional connections, so that replication can be more efficient and responsive.

This work introduces three closely related components:

1. **A generic bidirectional streaming wrapper** — A reusable component that manages the lifecycle of a bidirectional stream connection. It should lazily initialize the connection on first use, remain idempotent if initialized multiple times, automatically mark the connection as closed when a send or receive error occurs, and deliver received messages via a Go channel that is closed when the upstream connection ends.

2. **A task progress tracker** — A component that tracks in-flight replication tasks and computes the current low-watermark position (i.e., the highest position that has been fully processed). It must support adding batches of tasks while deduplicating tasks already seen, and must correctly derive the low-watermark based on whether the leading task is complete, cancelled, or still pending.

3. **A stream receiver** — A component that ties the above two together: it receives incoming replication messages over a stream, submits each task to a scheduler, and periodically acknowledges the current progress back to the source cluster using the low-watermark position.

## Expected Behavior

- The bidirectional stream wrapper must expose send, receive, and close operations; errors during send or receive must transition the stream to a closed state.
- The task tracker must maintain an ordered queue of tasks, advance past acknowledged tasks when computing the low-watermark, and return a position anchored to the first unfinished task when tasks are still in flight.
- When no progress has been made (empty queue), the tracker must indicate that no acknowledgment should be sent.
- The stream receiver must convert incoming wire-format messages into executable tasks and send acknowledgment messages that carry the current watermark position and timestamp.

## Why This Matters

Without this infrastructure, the replication subsystem cannot use persistent streams for cross-cluster data transfer. These components are the building blocks needed to support efficient, low-latency replication with proper back-pressure and progress tracking.
