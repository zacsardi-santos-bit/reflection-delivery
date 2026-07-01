## Description

The mesh service currently presents an inaccurate view of layer data to API clients. When clients query layer information, they receive all blocks that were part of a layer rather than the single block that was actually verified and applied. Similarly, account mesh data queries and streams incorrectly mix ATX activation events in with transaction data, even when clients are only interested in transactions.

## Expected Behavior

- The layer data returned by the mesh service should reflect only the single verified (applied) block per layer, not all blocks in the layer.
- Account mesh data queries and streams should return only transaction data. Activation (ATX) events should not be included in account mesh data responses.
- The mesh layer component should provide a way to look up the specific block that was applied for a given layer. If no block has been applied for that layer, the lookup should return nothing (not an error).
- When reading layer data, if the underlying layer lookup fails, the error should be reported clearly. If transaction data cannot be fully retrieved, that should also be reported as an error. However, if only the state root is unavailable, that should be treated as non-fatal and the layer should still be returned.

## Why This Matters

Clients relying on the API to understand the confirmed state of the network currently receive misleading data — multiple blocks where only one was actually accepted, and activation noise mixed into account history. Fixing this gives API consumers an accurate picture of which block was finalized per layer and clean transaction-only account data.
