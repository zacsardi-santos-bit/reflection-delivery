## Refactor: Replace Legacy Cell Notifications with Atomic Document Transactions in Code Mode

### Description

The code editing context currently uses two separate notification messages to communicate notebook changes to the frontend: one for cell code and stale/fresh status, and a second for cell ordering. Additionally, the context maintains its own private module-level dictionary to track cell names across invocations, separate from the notebook document model that already exists in the system.

This fragmented approach causes several problems:
- The frontend has to piece together information from multiple messages to understand what changed
- The code editing context duplicates state management that is already handled by the notebook document model
- Cells without names report an absent/missing value for their name field, which is inconsistent with the rest of the system

### Expected Behavior

- The code editing context should accept a notebook document snapshot from an external source (a context variable set by the caller) rather than building its own internal cell state
- All changes from a batch of operations should be communicated to the frontend as a single atomic transaction notification, describing precisely what happened: which cells were created, deleted, had code changed, had configuration changed, and the final cell ordering
- When a cell has no name, its name field should be an empty string, not an absent or missing value
- The old separate code-update and ordering notifications should no longer be sent for these operations

### Why This Matters

This makes the frontend communication cleaner and more atomic — the frontend receives one message describing exactly what changed, rather than having to reconcile multiple partial updates. It also removes the code editing context's private name tracking, which was a source of subtle state management bugs when running multiple batches in sequence.
