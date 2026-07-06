Implement a mechanism to replace 3D convolutional layers with linear transformations in vision models during inference. Enable users to opt into this "patch embedding fusion" when loading pretrained models, allowing for automatic conversion of checkpoint weights. Ensure the system handles edge cases gracefully and supports bidirectional weight conversion.

Requirements:
*   Implement the `Conv3dToLinear` class in `src/transformers/core_model_loading.py`:
    *   Construct with `in_channels` (int) and `kernel_size` (3-tuple of ints).
    *   Implement `convert(input_dict, source_patterns, target_patterns, **kwargs)` to reshape a 5D Conv3d weight tensor to a 2D tensor.
    *   Provide a `reverse_op` property returning a `LinearToConv3d` instance with the same parameters.
*   Implement the `LinearToConv3d` class in `src/transformers/core_model_loading.py`:
    *   Construct with `in_channels` (int) and `kernel_size` (3-tuple of ints).
    *   Implement `convert(input_dict, source_patterns, target_patterns, **kwargs)` to reshape a 2D linear weight tensor back to a 5D tensor.
    *   Provide a `reverse_op` property returning a `Conv3dToLinear` instance with the same parameters.
*   Ensure both classes inherit from `ConversionOps` and are exported from the `transformers.core_model_loading` module.
*   Define `_FUSION_DISCOVERY_CACHE` in `src/transformers/fusion_mapping.py` as a mutable cache (dict) for test isolation.
*   Implement `register_fusion_patches` in `src/transformers/fusion_mapping.py`:
    *   Accept a model class (`cls`), a config, and an optional `fusion_config`.
    *   Register fusions for models with Conv3d modules where stride equals kernel size, and other conditions are met.
    *   Register one monkey-patch and two checkpoint conversion mappings for compatible models.
    *   Skip registration if no compatible modules are found.
    *   Raise `ValueError` if a conflicting weight transformation is already registered, with a message containing 'conflicts with an existing conversion mapping'.
*   Update `from_pretrained` method in `src/transformers/modeling_utils.py`:
    *   Accept a `fusion_config` parameter to persist and apply fusion settings.
    *   Automatically re-apply fusion on subsequent loads if `fusion_config` is present in the model config.
    *   Ensure the model's patch embedding module exposes a `linear_proj` attribute of type `nn.Linear` after fusion.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.