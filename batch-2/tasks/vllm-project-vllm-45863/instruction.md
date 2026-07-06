Implement a caching mechanism in the `_build_sparse_index_metadata` method of the `DeepseekV4FlashInferMLAAttention` class to optimize the construction of mixed sparse token index tables. Ensure that repeated calls with identical batch metadata reuse previously computed results, except in specified conditions.

*   Update the `_build_sparse_index_metadata` method in `vllm/models/deepseek_v4/nvidia/flashinfer_sparse.py` to include caching logic.
    *   Cache the computed sparse index tensors when `swa_only=True` and the method is called with the same `swa_metadata` object.
    *   Apply the same caching behavior when `swa_only=False` and `self.compress_ratio == 128`, ensuring repeated calls with the same `swa_metadata` object return cached tensor objects.
    *   Skip caching when `swa_only=False` and `self.compress_ratio != 128`. In this case, ensure each call independently invokes the index builder and returns newly created tensor objects.
*   Ensure the method returns a 4-tuple, with the third element as `sparse_indices` and the fourth as `sparse_topk_lens`.
    *   For cached modes, return the same tensor objects by identity.
    *   For uncached modes, return fresh tensor objects.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.