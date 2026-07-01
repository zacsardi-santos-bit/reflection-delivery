## Description

The data node's segment sync mechanism is missing a proper intermediate lifecycle state when segments are marked for flushing. Currently, when a flush is triggered, segments are immediately transitioned to a "flushing" state, bypassing any intermediate "sealed" state. This means the system has no way to distinguish between segments that have been requested for flush and segments that are actively being flushed.

## Expected Behavior

- When a flush is requested for segments, they should first be transitioned to a "sealed" intermediate state.
- A dedicated sync policy should monitor for segments in the sealed state, transition them to the flushing state, and schedule them for syncing.
- The sync policy used in the write buffer's default configuration should be updated to use this new sealed-state-based approach rather than the old flushing-state-based approach.

## Why This Matters

Without a proper intermediate state, the system cannot accurately track where a segment is in its lifecycle between "active" and "actively flushing." Adding a sealed state allows better observability and more correct state transitions during data node operations, reducing the risk of incorrect behavior when segments transition through flush stages.
