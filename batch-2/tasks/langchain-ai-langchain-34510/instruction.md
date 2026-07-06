Implement a validation layer to protect against SSRF attacks during the deserialization of cloud AI model configurations. Ensure that the system blocks dangerous URL-override parameters and raises appropriate errors when such parameters are detected.

*   Create a new module at `langchain_core/load/validators.py` to include:
    *   A `CLASS_INIT_VALIDATORS` dictionary mapping class-path tuples to validator functions.
    *   A `_bedrock_validator(class_path: tuple[str, ...], kwargs: dict[str, Any]) -> None` function to check for dangerous parameters.
        *   Raise a `ValueError` if `endpoint_url` is present in `kwargs`, with a message matching `endpoint_url.*SSRF`.
        *   Raise a `ValueError` if `base_url` is present in `kwargs`, with a message matching `base_url.*SSRF`.
        *   If both `endpoint_url` and `base_url` are present, raise a `ValueError` with a message containing both parameter names and "SSRF".
        *   Do not raise an exception if `kwargs` only contains safe parameters.

*   Ensure `CLASS_INIT_VALIDATORS` includes `_bedrock_validator` for the following class-path tuples:
    *   `('langchain', 'chat_models', 'bedrock', 'BedrockChat')`
    *   `('langchain', 'chat_models', 'bedrock', 'ChatBedrock')`
    *   `('langchain', 'chat_models', 'anthropic_bedrock', 'ChatAnthropicBedrock')`
    *   `('langchain_aws', 'chat_models', 'ChatBedrockConverse')`
    *   `('langchain', 'llms', 'bedrock', 'Bedrock')`
    *   `('langchain', 'llms', 'bedrock', 'BedrockLLM')`
    *   `('langchain_aws', 'chat_models', 'bedrock_converse', 'ChatBedrockConverse')`
    *   `('langchain_aws', 'chat_models', 'anthropic', 'ChatAnthropicBedrock')`
    *   `('langchain_aws', 'chat_models', 'ChatBedrock')`
    *   `('langchain_aws', 'llms', 'bedrock', 'BedrockLLM')`

*   Update the `load()` function to:
    *   Automatically run any registered class-specific validator from `CLASS_INIT_VALIDATORS` for a matching class path before class instantiation.
    *   Ensure the class-specific validator executes even when `init_validator=None`.
    *   Call both the class-specific validator and a general `init_validator` if both are present, with the class-specific validator running first.

*   Extend `SERIALIZABLE_MAPPING` in `langchain_core/load/mapping.py` to include:
    *   `('langchain_aws', 'chat_models', 'ChatBedrockConverse')` mapping to the resolved `bedrock_converse` path.
    *   `('langchain', 'llms', 'bedrock', 'BedrockLLM')` mapping to the `langchain_aws` resolved path.

*   Update the `Reviver` class in `libs/core/langchain_core/load/load.py` to:
    *   Check `CLASS_INIT_VALIDATORS` and call the matching validator before `self.init_validator`.
    *   Ensure the class-specific validator is called even if `self.init_validator` is `None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.