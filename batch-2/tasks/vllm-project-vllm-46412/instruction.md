Implement a delta-save mechanism for the KV cache store component to optimize processing and ensure data persistence under memory pressure. Track the progress of each request to process only new tokens and handle skipped batches correctly.

*   Update `ChunkedTokenDatabase`:
    *   Modify `process_tokens` to yield tuples of `(start_idx: int, end_idx: int, block_hash)` where `block_hash` is a raw bytes-like object.
    *   Accept new keyword-only parameters: `chunk_mask` (list[bool] | None, default None), `put_step` (int, default 1), and `put_step_rank` (int, default 0).
    *   Apply filtering using `chunk_mask` and `put_step` before accessing chunk hashes.
    *   Compute `start_chunk` using ceiling division, indexing `chunk_mask` relative to `start_chunk`.
    *   Implement `key_for(chunk_hash: BlockHash) -> str` to return the full pool key string for a given block hash.

*   Update `MooncakeStoreCoordinator`:
    *   Modify `store_mask` to accept `start_token` as an optional second argument. Return masks for the suffix starting at `start_token`.
    *   Ensure masks are prefix-consistent across different token lengths.

*   Update `KVCacheStoreSendingThread`:
    *   Add `_saved_offset: dict[str, int]` to track the high-water mark of tokens persisted per request.
    *   Use `_saved_offset` to determine `save_start` for each request and pass it as `start_token` to `store_mask` and `mask_num` to `process_tokens`.
    *   Pass `chunk_mask=store_masks[g_idx]`, `put_step=self.put_step`, and `put_step_rank=(self.tp_rank + g_idx) % self.put_step` to `process_tokens`.
    *   Do not update `_saved_offset` when a batch is skipped due to pressure.
    *   Update `_saved_offset[req_id]` to `token_len` when a batch completes successfully.
    *   Clear `_saved_offset` for a request when it is removed via `remove_stored_request`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.