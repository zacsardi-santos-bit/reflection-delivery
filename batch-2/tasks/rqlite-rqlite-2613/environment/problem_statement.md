## Description

The database checkpoint operation currently returns only a byte count and an error value to its callers. This is insufficient for higher-level code that needs to know *whether the checkpoint actually completed its work* — for example, whether the write-ahead log was fully flushed and truncated — as opposed to merely whether it finished without throwing an error.

Additionally, when a checkpoint is blocked by a concurrent reader, callers have no way to know the failure is transient. This makes it impossible for the caller to distinguish between a temporary contention failure (which should be retried) and a permanent error (which should be escalated).

## Expected Behavior

- The checkpoint operation should return richer result metadata alongside the existing byte count and error values.
- The result metadata should expose a method indicating whether the checkpoint fully succeeded (i.e., the WAL was truncated).
- The result metadata should be loggable/printable in error messages.
- When a checkpoint is blocked by a concurrent reader, the returned error should be explicitly tagged as retryable so callers can differentiate transient from permanent failures.
- Repeated checkpoint calls against an already-empty write-ahead log should succeed and report success, not fail or return ambiguous results.

## Why This Matters

Higher-level systems that orchestrate checkpointing (e.g., snapshotting logic) need to know whether a checkpoint truly succeeded before proceeding. Without this information, a partially-completed checkpoint can silently lead to corrupted or incomplete snapshots.
