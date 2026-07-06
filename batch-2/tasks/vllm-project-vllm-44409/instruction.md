Implement a fix for the memory-safety bug in the KV cache manager used in hybrid attention models with multiple KV cache groups. Ensure that block allocation ordering is corrected to prevent duplicate block IDs and corrupted reference counts across groups. Separate the responsibilities of local and external block allocation into distinct methods.

Requirements:
*   Ensure `KVCacheBlocks` is importable from `vllm.v1.core.kv_cache_manager` alongside `KVCacheManager` and `Request`.
*   Implement `KVCacheBlocks` to:
    *   Accept a tuple of per-group block sequences in its constructor.
    *   Provide a `get_block_ids()` method returning a tuple of integer block-ID lists, one list per group.
*   After calling `KVCacheManager.free(request)`, ensure `KVCacheManager.get_blocks(req_id).get_block_ids()` returns a tuple of empty lists matching the number of KV cache groups.
*   Ensure `KVCacheManager.allocate_slots()`:
    *   Returns unique block IDs across all groups.
    *   Ensures every block referenced by a request has a reference count of at least 1.
    *   Maintains these guarantees for both 2-group and 3-group configurations, including after a preempt-then-reallocate cycle.
*   After freeing and reallocating a request with the same ID, `KVCacheManager.get_blocks(req_id).get_block_ids()` must return non-empty results, maintaining the no-double-allocation guarantee.
*   Rename `allocate_new_computed_blocks` to `add_local_computed_blocks` in `SingleTypeKVCacheManager`:
    *   Ensure it is callable with positional `request_id` and `new_computed_blocks` arguments, plus a keyword `num_local_computed_tokens` argument.
*   Add a new method `allocate_external_computed_blocks` to `SingleTypeKVCacheManager`:
    *   Ensure it is called only after all groups complete `add_local_computed_blocks`.
    *   Use the signature: `allocate_external_computed_blocks(request_id: str, num_local_computed_tokens: int, num_external_computed_tokens: int) -> None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.