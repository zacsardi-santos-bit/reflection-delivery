## Description

We need to add coordinator-level support for safely finalizing async attached function invocations. Currently, there is no mechanism to atomically record that an async function has completed and simultaneously detect whether the underlying collection's log has moved ahead of the function's expected completion point, which would require a reconciliation step before the finalization is considered complete.

## Expected Behavior

- There should be an operation that attempts to finalize an async attached function's completion state. This operation should compare the collection's current log position against the requested new completion offset:
  - If the collection log is ahead of the new completion offset, the response should indicate that a repair is needed and include the current log position so the caller knows what offset to reconcile to.
  - If the collection log is at or behind the new completion offset, the response should indicate success and include the updated completion offset.
  - If the requested completion offset would move the stored value backward, the operation must be rejected with an error.
- There should be a separate operation to finalize the repair process once reconciliation is done. This simply marks the pending state as resolved.
- Both operations must be idempotent — calling them multiple times with the same arguments must produce the same outcome each time.

## Why This Matters

Without these operations, async functions that process data from a collection have no safe way to record their progress or handle the scenario where data ingestion races ahead of function processing. This leads to state inconsistencies that are hard to recover from. The idempotency guarantee is critical for reliability in distributed systems where operations may be retried.
