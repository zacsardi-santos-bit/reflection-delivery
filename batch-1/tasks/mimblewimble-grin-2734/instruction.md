Implement a configurable storage backend for the Merkle Mountain Range (MMR) that supports both fixed-size and variable-size elements. Correct existing bugs related to logical size tracking, Merkle root hash consistency after rewinding, and storage file state after full pruning and compaction.

*   Update the PMMRBackend constructor in `store/src/pmmr.rs`:
    *   Add a boolean parameter `fixed_size` as the third argument in the signature: `new(data_dir: P, prunable: bool, fixed_size: bool, header: Option<&BlockHeader>) -> io::Result<PMMRBackend<T>>`.
    *   Ensure the backend supports variable-size elements when `fixed_size` is false, maintaining functionality for all PMMR operations.

*   Ensure logical size consistency:
    *   Maintain the stability of the PMMR's logical size across sync, prune, and compact operations.
    *   Verify that `PMMRBackend::unpruned_size()` returns the correct total node count after these operations.

*   Implement correct data retrieval:
    *   Retrieve leaf data by 1-indexed position, returning the stored element for valid positions.
    *   Return nothing for non-leaf, pruned, or out-of-range positions.

*   Implement correct hash retrieval:
    *   Retrieve hashes for both leaf and internal nodes by 1-indexed position.
    *   Ensure parent nodes return hashes computed from their children.

*   Ensure Merkle root hash consistency:
    *   After rewinding to a previous position, the computed Merkle root must match the historical root hash for that state.

*   Implement the `hash_size` method in `store/src/pmmr.rs`:
    *   Signature: `hash_size(&self) -> u64`.
    *   Return the number of hash entries currently stored in the hash file.
    *   After a full prune, compact, and rewind cycle, ensure `hash_size()` returns 1.

*   Ensure correct data element count after full lifecycle:
    *   After pruning all elements, compacting, and rewinding to an empty state, `data_size()` must return 0.
    *   Data retrieval for positions 1 through 16 must return nothing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.