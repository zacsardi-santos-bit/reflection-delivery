## Description

We need a durable, file-backed queue for the Prometheus remote write pipeline. Currently there is no mechanism to persist in-flight metrics data to disk, so if the process crashes or is restarted any buffered data is lost. The queue should support storing raw byte payloads alongside optional string key-value metadata, and should guarantee that previously stored entries survive a process restart.

## Expected Behavior

- Stored entries (data payload + optional metadata) must be written to disk immediately and delivered to a consumer callback in FIFO order.
- When the system restarts and a new queue is opened on the same directory, all previously committed but unconsumed entries must be replayed in their original order before any new entries are delivered.
- Files present in the queue directory that do not belong to the queue should be silently ignored.
- If a stored file is corrupted or has been deleted by the time the consumer tries to read it, the error must be surfaced to the consumer for that specific item; all other items must continue to be delivered normally.
- The queue must clean up any background resources fully when stopped, leaving no goroutine leaks.

## Why This Matters

A file-backed queue with restart-durability and graceful error handling is essential for reliable remote write behavior. Without it, a process restart during a write burst drops metrics silently. With it, the pipeline can recover from crashes and continue from where it left off.
