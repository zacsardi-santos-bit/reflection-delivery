## Description

The container Merkle tree used in Ozone's data integrity system has no way to record whether individual chunks are healthy or corrupted. When a scanner detects that a chunk is bad, this health state cannot currently be captured in the tree structure — all chunks appear as implicitly healthy regardless of their actual condition. This limits the usefulness of the Merkle tree for reconciliation workflows, which need to know which chunks are corrupted in order to repair them correctly.

## Expected Behavior

- When adding chunks to a container Merkle tree, callers should be able to specify whether the chunks are healthy or unhealthy. This health status should be preserved in the serialized tree representation.
- It should be possible to create a new Merkle tree writer from an already-serialized tree, so that an existing tree can be incrementally updated (e.g., to add newly discovered empty blocks or newly scanned chunks) without rebuilding from scratch.
- Empty blocks should be representable in the tree and preserved during serialization round-trips.
- The method used to build a container Merkle tree from stored block metadata should have a name that clearly distinguishes it from a live scan-based tree builder.
- The container set should support registering an on-demand scanner callback so that reconciliation operations can trigger automatic re-scans.

## Why This Matters

Without chunk-level health tracking in the Merkle tree, the reconciliation process cannot tell which chunks need to be repaired versus which are healthy. Adding this flag enables accurate, targeted repair during container reconciliation and makes the overall data integrity story more complete.
