Implement support for the FalconMamba model architecture in the Transformers library. Ensure that the model can be loaded from pretrained checkpoints, used for text generation, and integrated with existing library infrastructure. Implement configuration and model classes, and ensure compatibility with feature-extraction and text-generation pipelines.

*   Implement `FalconMambaConfig`:
    *   Location: `src/transformers/models/falcon_mamba/configuration_falcon_mamba.py`
    *   Inherit from `PretrainedConfig`.
    *   Set `model_type = 'falcon_mamba'`.
    *   Expose attributes: `hidden_size`, `num_hidden_layers`, `state_size`, `conv_kernel`, `intermediate_size`, `time_step_min`, `time_step_max`, `time_step_floor`.
    *   Constructor must accept additional arguments via `**kwargs`.
    *   Register in the auto configuration system.

*   Implement `FalconMambaPreTrainedModel`:
    *   Location: `src/transformers/models/falcon_mamba/modeling_falcon_mamba.py`
    *   Inherit from `PreTrainedModel`.
    *   Set `config_class = FalconMambaConfig`, `base_model_prefix = "backbone"`, `supports_gradient_checkpointing = True`, `_is_stateful = True`.
    *   Implement `_init_weights` to initialize `dt_proj.bias`, `A_log`, and `D`.

*   Implement `FalconMambaModel`:
    *   Location: `src/transformers/models/falcon_mamba/modeling_falcon_mamba.py`
    *   Inherit from `FalconMambaPreTrainedModel`.
    *   Expose `embeddings` and `layers` attributes.
    *   Implement `forward` method with specified parameters and return types.

*   Implement `FalconMambaForCausalLM`:
    *   Location: `src/transformers/models/falcon_mamba/modeling_falcon_mamba.py`
    *   Inherit from `FalconMambaPreTrainedModel`.
    *   Expose `backbone` attribute and set `_tied_weights_keys = ["lm_head.weight"]`.
    *   Implement `forward` method to compute cross-entropy loss and return logits.
    *   Register with `AutoModelForCausalLM`.

*   Implement `FalconMambaMixer`:
    *   Location: `src/transformers/models/falcon_mamba/modeling_falcon_mamba.py`
    *   Expose `mixer` attribute in `FalconMambaBlock`.
    *   Implement `slow_forward` method.

*   Implement `FalconMambaOutput` and `FalconMambaCausalLMOutput`:
    *   Location: `src/transformers/models/falcon_mamba/modeling_falcon_mamba.py`
    *   Subclass `ModelOutput`.
    *   Define required fields.

*   Ensure caching works correctly:
    *   Sequential token processing with caching must match full sequence processing.

*   Registration and export:
    *   Export classes from `src/transformers/models/falcon_mamba/__init__.py`.
    *   Update `src/transformers/__init__.py` and `src/transformers/models/__init__.py`.
    *   Register in `configuration_auto.py` and `modeling_auto.py`.
    *   Add dummy classes in `dummy_pt_objects.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.