## Description

The managed ledger module lacks a reusable, general-purpose utility for replaying all entries from a cursor from start to finish. Currently, each component that needs to replay cursor entries has to implement its own read loop with manual batch handling, callback management, and error propagation — leading to duplicated, error-prone code.

We need a standard replay task that can be configured with a batch size and an executor, reads entries from a cursor in batches until the end of the ledger, calls a user-provided processor for each entry, and returns the position of the last successfully processed entry. The component must:

- Handle partial failures gracefully: if the processor rejects an entry (e.g., throws an exception), replay should stop and return the last position that was successfully processed rather than failing entirely.
- Propagate unexpected read errors (e.g., cursor operations failing) to the caller via a future that completes exceptionally.
- Track how many entries have been processed, so callers can decide whether to take a snapshot or perform other post-replay actions.
- Release entry buffers correctly after processing so there are no memory leaks.

## Expected Behavior

- When the cursor has no entries to replay, the result indicates no entries were processed.
- When entries are present, they are processed in batches; the last processed position is returned.
- If the user-provided processor throws an exception on an entry, processing stops at the previous successful entry.
- If the underlying cursor read operation itself throws, the replay future fails with that exception.
- The count of processed entries is available after replay completes.

## Why This Matters

Without a shared replay utility, subsystems that need to recover state from a managed ledger (such as the deduplication system) must each manage their own low-level cursor replay logic. This makes it hard to fix bugs consistently, add features, or handle edge cases uniformly. A well-tested, reusable component reduces code duplication and improves reliability.
