## Description

The reduce pipeline's data-forwarding component currently has no direct access to the persistent store manager — it is embedded inside the partition buffer queue manager instead. This means the same store manager cannot be cleanly shared between both components. We need the data-forwarding component to accept the store manager as an explicit, independent dependency so both can share a single instance.

Additionally, the current storage interfaces have ergonomic issues:

- **Batch-oriented replay**: Replaying persisted messages from a store requires specifying a count upfront and returns a slice plus an end-of-file boolean. This is awkward because callers must guess the batch size and loop appropriately. Replay should instead stream messages asynchronously through channels, closing them when done.
- **Discovery returns identifiers, not objects**: When discovering existing stores (e.g., on startup for crash recovery), the API returns only partition identifiers rather than usable store objects. Callers then have to perform extra lookups to obtain actual stores. Discovery should return ready-to-use store instances directly.
- **No persistence control on write**: Writing to the buffer queue has no way to specify whether the message should also be persisted to backing storage. This matters during replay, where messages should not be re-persisted. A boolean flag is needed to make this distinction explicit.

## Expected Behavior

- The data forwarding constructor accepts the store manager as a separate parameter from the buffer queue manager.
- Store discovery returns a list of usable store objects, not just identifiers. After creating stores, discovery returns them; after deletion, discovery returns nothing.
- Replay streams messages through channels and closes them when complete. An empty store's replay closes immediately with zero messages.
- Writing to the buffer queue accepts a boolean flag to control persistence.
- The replay responsibility moves from the buffer queue manager level to the individual store level.

## Why This Matters

These changes make the storage layer more composable and the interfaces more idiomatic. Sharing a single store manager instance across components reduces duplication. Channel-based replay allows consumers to process messages as they arrive without predetermining batch sizes. Returning store objects from discovery eliminates extra indirection steps during crash recovery.
