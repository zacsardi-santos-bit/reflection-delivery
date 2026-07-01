## Description

The Merkle Mountain Range (MMR) backend storage currently assumes all stored elements have a fixed, compile-time-known size in bytes. This is a hidden limitation that prevents the system from correctly handling data types whose byte representation varies in size. We need to make the backend configurable so callers can specify whether their element type is fixed-size or variable-size.

In addition to this core flexibility gap, there are several correctness bugs that have been discovered:

- The reported logical size of the MMR structure drifts unexpectedly after pruning entries and compacting the underlying files, meaning the system no longer correctly tracks how many nodes the full (un-compacted) tree would contain.
- After rewinding the MMR to a previously recorded position, the Merkle root hash computed from that state is incorrect, breaking the ability to verify historical consistency.
- After fully pruning all elements, compacting, and rewinding to an empty state, the underlying storage files report incorrect sizes rather than cleanly reflecting that no data remains.

## Expected Behavior

- The backend constructor should accept a parameter indicating whether elements are stored as fixed-size or variable-size, so the appropriate storage strategy can be selected at construction time.
- The logical size of the MMR (the total count of nodes in the full tree) must remain stable across any combination of pruning, compaction, and syncing operations.
- Rewinding the MMR to any prior position must correctly restore the Merkle root for that historical state.
- After a complete cycle of pruning all entries, compacting, and rewinding to the beginning, both the hash storage and data storage must report zero elements remaining (or the minimal expected state).

## Why This Matters

This affects the correctness and completeness of the MMR as a cryptographic data structure. An incorrect root hash after rewind breaks proof verification. A drifting size count breaks all size-dependent logic. And the fixed-size constraint limits future use of the storage backend with variable-length data types like block headers.
