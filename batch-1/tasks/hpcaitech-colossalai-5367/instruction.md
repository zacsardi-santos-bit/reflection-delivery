Implement a new batch management system for language model inference that efficiently groups sequences for prefill and decode operations. Develop a BatchBucket class to manage sequence batches and a KVCacheManager for memory allocation. Update sequence handling to remove internal block table management.

*   Create a `BatchBucket` class in `colossalai/inference/batch_bucket.py`:
    *   Constructor must accept `num_heads`, `head_size`, `max_batch_size`, `max_length`, `block_size`, and `kv_max_split_num`.
    *   Implement `add_seqs(seqs, alloc_block_tables_fn=None)` to return a `torch.Tensor` of negative values and optionally call `alloc_block_tables_fn`.
    *   Expose `seq_lengths` as a `torch.Tensor` reflecting updated sentence lengths.
    *   Implement properties: `current_batch_size`, `seqs_li`, `block_tables`, and `is_compact`.
    *   Implement `append_batch_tokens(tokens)` to update sequence lengths.
    *   Implement `pop_seq_update_batch(request_id, free_block_table_fn=None)` to remove sequences and maintain compactness.
    *   Implement `merge(other_bucket)` to combine batches, returning unmerged request IDs.
    *   Implement `clear(free_block_tables_fn=None)` to reset the batch and optionally free block tables.

*   Update `KVCacheManager` in `colossalai/inference/kv_cache/kvcache_manager.py`:
    *   Expose `max_blocks_per_sequence` and `get_head_size()`.
    *   Implement `allocate_context_from_block_tables(block_tables, context_lengths)` for prefill allocation.
    *   Implement `allocate_tokens_from_block_tables(block_tables, context_lens, bsz=None)` for decode allocation.
    *   Implement `free_block_table(block_table)` and `free_block_tables(block_tables, first_n=None)` to free memory.
    *   Ensure `_cache_blocks` list of objects has `available_space` attribute reset after freeing.

*   Modify `RunningList` in `colossalai/inference/core/request_handler.py`:
    *   Implement `mark_prefill_running()` to set sequences to `RequestStatus.RUNNING`.
    *   Implement `move_prefill_to_decoding(seq_ids)` to transition sequences from prefill to decoding.

*   Update `Sequence` class in `colossalai/inference/struct.py`:
    *   Remove any `block_table` field or constructor parameter.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.