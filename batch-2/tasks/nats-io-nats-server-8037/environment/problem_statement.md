## Description

The file-based message store permanently disables writes when it encounters corrupt or unreadable on-disk data during an otherwise valid write operation. This is incorrect behavior: errors that arise from reading and decoding existing stored data (such as a corrupt message cache or a partially valid state file) are fundamentally different from errors that arise from writing new data. Currently, both types of errors are treated identically, causing the write path to shut itself down even when the storage device is healthy and the failure was a transient read-side issue.

## Expected Behavior

- Errors originating from reading or interpreting existing on-disk data should be recognized as distinct from write failures and should not permanently disable the write path.
- After encountering such a read-side error during a store operation, subsequent write operations should succeed normally.
- True write failures (e.g., from the underlying I/O layer) must still permanently disable writes as before.
- When rebuilding the internal per-subject message index fails partway through, the partially-built index and the corrupt in-memory cache must both be discarded, so the next attempt starts clean from disk rather than reusing incomplete or corrupt in-memory state.

## Why This Matters

A single transient data corruption event — something that only affects the ability to read back a specific message — can permanently freeze message ingestion for an entire stream. Since writes to healthy storage are completely unaffected by a corrupt read cache, blocking all further writes is far too aggressive and causes unnecessary data-loss risk or stream unavailability.
