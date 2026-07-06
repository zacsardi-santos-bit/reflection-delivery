# Add Chain-Aware Routing and Persistent Head Tracking to the Supervisor Backend

## Description

The supervisor component manages state for multiple blockchain networks simultaneously, but its internal storage and processing layers are not organized around the concept of individual chains. Operations that should target a specific chain have no chain context attached, making it impossible for a central coordinator to route them correctly or to enforce isolation between chains.

Two related gaps need to be addressed:

1. There is no central database layer that can receive a log-recording or rewind request along with a chain identifier and dispatch it to the correct per-chain storage. Without this, callers have to manage routing themselves, and there is no way to return a clear error when an unknown chain is referenced.

2. There is no persistent tracking of the various "head" block pointers for each chain (unsafe, cross-validated unsafe, locally safe, cross-safe, locally finalized, and cross-finalized). These heads need to survive process restarts, and updates to them must be atomic — if an update fails midway (either because the operation itself fails or because the data cannot be flushed to disk), the previous state must be fully preserved.

## Expected Behavior

- A multi-chain database layer routes log-storage and rewind operations to the correct per-chain storage based on chain identity, and returns a clear "unknown chain" error for unrecognized chains.
- A persistent head tracker records all chain head pointers to disk and reloads them on startup; it applies updates atomically and rolls back completely on any failure.
- The processing pipeline components carry chain identity with each operation they produce.
- The per-chain log database is organized into its own sub-package, separate from the multi-chain coordination layer.
- The database recovery function reports only success or failure, without returning a block number.

## Why This Matters

Without these changes, the supervisor cannot scale to multiple chains in a safe, well-organized way. Adding chain-awareness throughout the stack ensures operations are always routed correctly, head state survives restarts, and partial failures leave the system in a consistent state.
