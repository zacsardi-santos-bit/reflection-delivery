I'm working on the NIXL-backed storage layer for a high-priority cache system.

*   HiCacheNixl must not leak file descriptors during cache operations. After calling get, batch_set, or batch_get, the number of open file descriptors must equal the count before the call.

*   The internal transfer method in HiCacheNixl must close all file descriptors opened for file-backed transfers in a finally block, ensuring cleanup happens even when errors occur.

*   HiCacheNixl must be initializable without a plugin parameter; plugin selection and configuration must be conveyed entirely through the storage_config object's extra_config field.

*   HiCacheStorageConfig must accept pp_rank (int), pp_size (int), attn_cp_rank (int), attn_cp_size (int), enable_storage_metrics (bool), and extra_config (Optional[dict]) parameters.

*   The extra_config parameter of HiCacheStorageConfig must accept nested plugin configuration dictionaries of the form {"plugin": {"<plugin_name>": {"active": True}}}.

*   HiCacheNixl.register_files must accept a list of file path strings (List[str]) directly and return a non-None result when registration succeeds.


*   Interface details: Type: Class
Name: HiCacheStorageConfig
Location: python/sglang/srt/mem_cache/hicache_storage.py
Description: Configuration dataclass for HiCache storage backends. Must be extended to accept the following additional fields.
Signature:
  HiCacheStorageConfig(
      tp_rank: int,
      tp_size: int,
      pp_rank: int,
      pp_size: int,
      attn_cp_rank: int,
      attn_cp_size: int,
      is_mla_model: bool,
      is_page_first_layout: bool,
      model_name: Optional[str],
      enable_storage_metrics: bool,
      extra_config: Optional[dict] = None,
  )

Type: Class
Name: HiCacheNixl
Location: python/sglang/srt/mem_cache/storage/nixl/hicache_nixl.py
Description: NIXL-backed HiCache storage implementation. The constructor must NOT include a plugin parameter; plugin configuration must be sourced from storage_config.extra_config.
Signature:
  __init__(self, storage_config: HiCacheStorageConfig, file_path: str) -> None

  register_files(self, file_paths: List[str]) -> Any
    Accepts a list of file path strings directly (not pre-converted NIXL tuples).
    Returns a non-None value on success.

  get(self, key: str, dst: torch.Tensor) -> torch.Tensor
    Must not leak file descriptors; open fd count must be identical before and after the call.

  batch_set(self, keys: List[str], values: List[torch.Tensor]) -> bool
    Must not leak file descriptors; open fd count must be identical before and after the call.

  batch_get(self, keys: List[str], dsts: List[torch.Tensor]) -> List[torch.Tensor]
    Must not leak file descriptors; open fd count must be identical before and after the call.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.