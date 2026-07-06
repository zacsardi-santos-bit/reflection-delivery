## Description

The KV cache store component currently re-processes all blocks from the beginning of every request on each successive save batch. For long-context requests that arrive in multiple chunks, this means blocks already persisted in an earlier batch are redundantly re-checked on every subsequent batch. More critically, when a save batch is bypassed due to CPU or disk pressure, there is no mechanism to retry the skipped range — those blocks are silently lost and never stored.

We need a delta-save approach: track how far along each request has been successfully persisted, and on each new batch, process only the new suffix of tokens that haven't been saved yet. When a batch is skipped due to pressure, the saved-progress marker must stay at its previous value so the next batch automatically covers the missed range.

## Expected Behavior

- The store thread maintains a per-request saved-progress tracker (high-water mark of tokens persisted)
- Each store batch processes only tokens beyond the previously saved point
- The per-group mask computation supports computing masks for a partial suffix starting at a given token offset, so incremental saves correctly compute which chunks of the new suffix need to be stored
- When a batch is skipped due to pressure, the progress marker is not advanced — the next batch retries the skipped range from the last saved point
- When a request is removed, its progress tracking is also cleaned up

## Why This Matters

Without incremental tracking, long-context requests incur growing overhead on every batch as the saved range is repeatedly re-scanned. More importantly, under memory pressure where batches are skipped, KV cache blocks can be permanently lost, breaking prefill caching for subsequent requests that share a common prefix.
