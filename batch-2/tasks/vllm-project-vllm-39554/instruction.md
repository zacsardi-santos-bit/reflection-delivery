I'm running into an inconsistency when loading model checkpoints with a custom config override.

*   When get_config is called with an hf_overrides_kw that specifies a model_type registered in _CONFIG_REGISTRY, and the on-disk model_type in the checkpoint's config file differs from the overridden model_type, get_config must return an instance of the config class registered in _CONFIG_REGISTRY under the overridden model_type — not the class normally associated with the on-disk model_type.

*   When such an override is applied and the on-disk model_type differs from the overridden model_type, the custom config class must be registered in HuggingFace's auto-config mapping under both the overridden model_type key and the on-disk model_type key.

*   After get_config is called with a model_type override that conflicts with the on-disk model_type, calling AutoConfig.from_pretrained on the same checkpoint directory must return an instance of the custom config class (not the class normally associated with the on-disk model_type).


*   Interface details: Type: Function
Name: get_config
Location: vllm/transformers_utils/config.py
Signature: get_config(model_name_or_path: str, trust_remote_code: bool, revision: Optional[str] = None, code_revision: Optional[str] = None, config_format: Any = None, hf_overrides_kw: Optional[dict] = None, **kwargs) -> PretrainedConfig
Description: Loads and returns a HuggingFace PretrainedConfig for the given model path. When hf_overrides_kw contains a "model_type" key that is registered in _CONFIG_REGISTRY, and the on-disk model_type in the checkpoint's config.json differs from the overridden model_type, get_config must return an instance of the config class registered under the overridden model_type and must register that class under both the overridden model_type and the on-disk model_type in HuggingFace's auto-config mapping.

Type: Variable
Name: _CONFIG_REGISTRY
Location: vllm/transformers_utils/config.py
Description: A module-level dictionary mapping model type strings to config classes (subclasses of PretrainedConfig). Used by get_config to resolve custom config classes when a model_type override is provided via hf_overrides_kw.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.