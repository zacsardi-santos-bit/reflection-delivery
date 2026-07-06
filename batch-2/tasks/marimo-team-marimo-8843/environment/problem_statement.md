## Description

The current mechanism for notifying the frontend (and any downstream consumers) about notebook structural changes is too coarse-grained. When cells are added, deleted, or updated, separate notifications are emitted carrying bulk state — full lists of codes, cell IDs, and configurations — rather than precise descriptions of *what changed*. This makes it difficult for receiving parties to reconstruct exactly what happened and apply those changes efficiently.

## Expected Behavior

- Structural operations on a notebook (create cell, delete cell, update code, update config, reorder) should be represented as discrete, typed operation records bundled together in a single transaction notification.
- A transaction notification should be serializable to plain structured data that consumers can inspect and act on.
- Each transaction must include the complete new ordering of cells as a reorder operation, so consumers can always reconstruct the current document order.
- The cell name field should consistently default to an empty string when no name has been set, so callers never need to handle an absent value.

## Why This Matters

With a structured, operation-based protocol, the frontend and other consumers can apply the exact operations that were performed rather than diffing old and new state. This enables cleaner replication, better collaboration support, and removes ambiguity about whether a change was an update or a structural reorganization.
