Implement improvements to the Helion kernel registration workflow to address usability issues. Ensure that kernel registration is a single-step process and handle missing configurations at setup time. Update the system to support autotuning for unsupported hardware while maintaining visibility in the global registry.

*   Update `HelionKernelWrapper`:
    *   Accept `config_picker` as a constructor parameter. Remove the `register_config_picker` method.
    *   Initialize eagerly during construction:
        *   Call `get_canonical_gpu_name` once in `__init__`.
        *   Build and store the configured kernel in `_configured_kernel` if configs are available.
    *   When no platform configs are found:
        *   Set `_disabled = True` and `_disabled_reason` to "No configs available".
        *   Do not raise an exception at construction.
    *   If `_disabled` is `True`:
        *   Raise `RuntimeError` with "is disabled" message when `__call__` or `get_configured_op()` is invoked.
        *   Ensure `get_inputs()` returns the result of `input_generator` without raising an error.
        *   Ensure `run_autotune(inputs)` calls `create_helion_decorated_kernel` and runs autotuning, returning a `helion.Config`.
    *   Ensure disabled wrappers are stored in the global `_REGISTERED_KERNELS` registry.

*   Update `register_kernel` function:
    *   Require `config_picker` as a keyword argument and pass it to `HelionKernelWrapper`.
    *   Raise `ValueError` with "already registered" if `op_name` is registered again.
    *   Raise `ValueError` with "uses a custom autotuner" if `helion_settings` contains a custom `autotuner_fn`.
    *   Ensure disabled wrappers are added to the global registry.

*   Implement `create_helion_decorated_kernel`:
    *   Make it importable from `vllm.kernels.helion.register`.
    *   Accept a raw kernel function and an optional `helion_settings` parameter.
    *   Ensure the returned object supports `.autotune(inputs)` returning a `helion.Config`.

*   Update `ConfigManager` class:
    *   Support direct instantiation via `ConfigManager(base_dir=path)`.
    *   Implement `reset_instance()` class method to clear any cached singleton state.

*   Ensure `run_autotune` on a disabled kernel produces a valid `helion.Config` equivalent to the default config from `create_helion_decorated_kernel`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.