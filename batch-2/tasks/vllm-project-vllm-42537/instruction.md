Implement a persistent cache for FlashInfer autotune results in vLLM to avoid redundant autotuning on each startup. Ensure the cache is stored in a stable, versioned directory and allow users to override the default cache location using an environment variable.

*   Add a function `_resolve_flashinfer_autotune_file(runner)` in `vllm/model_executor/warmup/kernel_warmup.py`:
    *   Return a `Path` pointing to 'autotune_configs.json' within a versioned, content-addressed cache directory.
    *   If `VLLM_FLASHINFER_AUTOTUNE_CACHE_DIR` is `None`, construct the path as: `Path(envs.VLLM_CACHE_ROOT) / 'flashinfer_autotune_cache' / <flashinfer_workspace.parent.name> / <flashinfer_workspace.name> / <cache_hash> / 'autotune_configs.json'`.
    *   If `VLLM_FLASHINFER_AUTOTUNE_CACHE_DIR` is set, construct the path as: `Path(VLLM_FLASHINFER_AUTOTUNE_CACHE_DIR) / <cache_hash> / 'autotune_configs.json'`.
    *   Compute `cache_hash` using `sha256(str(aot_compile_hash_factors(runner.vllm_config)).encode()).hexdigest()`.
    *   Ensure the directory containing 'autotune_configs.json' is created before returning the path.

*   Update module-level imports in `kernel_warmup.py`:
    *   Import `aot_compile_hash_factors` at the top level to be accessible as `kernel_warmup.aot_compile_hash_factors`.
    *   Import `vllm.envs` as `envs` to be accessible as `kernel_warmup.envs`.

*   Add `VLLM_FLASHINFER_AUTOTUNE_CACHE_DIR` in `vllm/envs.py`:
    *   Define it as an optional string environment variable with a default value of `None`.
    *   Read its value from the OS environment variable of the same name.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.