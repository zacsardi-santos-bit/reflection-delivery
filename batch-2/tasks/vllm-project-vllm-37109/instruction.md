Implement a dedicated key type for the KV offloading system to decouple it from the GPU block cache internals. Update the offloading manager interfaces to use this new key type, allowing for the distinction between blocks from different KV cache groups.

*   Define a new type `OffloadKey` in `vllm/v1/kv_offload/abstract.py` that is hashable and supports equality comparison.
    *   Use `make_offload_key(block_hash: bytes, group_idx: int) -> OffloadKey` as a factory function to create `OffloadKey` instances.
*   Update the `PrepareStoreOutput` dataclass in `vllm/v1/kv_offload/abstract.py`:
    *   Rename `block_hashes_to_store` to `keys_to_store: list[OffloadKey]`.
    *   Rename `block_hashes_evicted` to `evicted_keys: list[OffloadKey]`.
*   Modify the `OffloadingEvent` dataclass in `vllm/v1/kv_offload/abstract.py`:
    *   Rename `block_hashes` to `keys: list[OffloadKey]`.
*   Update the `OffloadingManager` abstract class in `vllm/v1/kv_offload/abstract.py`:
    *   Change all block-identifier parameters from `Iterable[BlockHash]` to `Iterable[OffloadKey]` for methods: `lookup`, `prepare_load`, `prepare_store`, `touch`, `complete_load`, `complete_store`.
*   Implement `CPUOffloadingManager` in `vllm/v1/kv_offload/cpu/manager.py`:
    *   Ensure all methods accept `OffloadKey` as the block-identifier type.
*   Update `FilterReusedOffloadingManager` in `vllm/v1/kv_offload/reuse_manager.py`:
    *   Ensure `counts` is an `OrderedDict[OffloadKey, int]`.
*   Modify `OffloadingConnectorScheduler` in `vllm/distributed/kv_transfer/kv_connector/v1/offloading/scheduler.py`:
    *   Expose a `config` attribute with `kv_group_configs` sequence, each containing `gpu_block_size: int` and `offloaded_block_size: int`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.