Implement a unified generator component for Haystack to interact with HuggingFace's inference services, supporting both serverless API and self-hosted server modes. Validate configuration parameters at startup, support streaming output, and ensure the component is serializable.

*   Implement `is_valid_http_url` in `haystack/utils/url_validation.py`:
    *   Return `True` for valid `http://` or `https://` URLs with a non-empty host.
    *   Return `False` for non-HTTP schemes, bare hostnames, URLs without a host, or empty strings.

*   Add `HFGenerationAPIType` enum in `haystack/utils/hf.py`:
    *   Include members: `SERVERLESS_INFERENCE_API` and `TEXT_GENERATION_INFERENCE`.
    *   Implement `from_str` to convert strings to enum members, raising `ValueError` for unrecognized strings.
    *   Implement `__str__` to return the enum's string value.

*   Implement `HuggingFaceAPIGenerator` in `haystack/components/generators/hugging_face_api.py`:
    *   Constructor (`__init__`):
        *   Convert `api_type` string using `HFGenerationAPIType.from_str`.
        *   For `SERVERLESS_INFERENCE_API`, raise `ValueError` if `model` is absent in `api_params` and propagate `RepositoryNotFoundError` if the model ID is invalid.
        *   For `TEXT_GENERATION_INFERENCE`, raise `ValueError` if `url` is absent in `api_params` or if the URL is invalid per `is_valid_http_url`.
        *   Merge `stop_words` into `generation_kwargs` as `stop_sequences`.
        *   Default `max_new_tokens` to 512 in `generation_kwargs`.
        *   Set instance attributes: `api_type`, `api_params`, `generation_kwargs`, `streaming_callback`, `token`.

    *   `run` method:
        *   Accept `prompt` and optional `generation_kwargs`.
        *   Merge runtime `generation_kwargs` with instance-level defaults.
        *   Pass `details=True` to `InferenceClient.text_generation`.
        *   If `streaming_callback` is set, pass `stream=True`, iterate response, and invoke callback per token.
        *   Return a dict with `replies` (list of strings) and `meta` (list of dicts).

    *   Serialization:
        *   `to_dict` method: Return a dict with `init_parameters` containing serialized `api_type`, `api_params`, `token`, and `generation_kwargs`.
        *   `from_dict` class method: Restore all attributes from the serialized dict, including `streaming_callback`.

*   Ensure `HuggingFaceAPIGenerator` is importable from `haystack.components.generators` by listing it in `__all__` and exporting from `__init__.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.