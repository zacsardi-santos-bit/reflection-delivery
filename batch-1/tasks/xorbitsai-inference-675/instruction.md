Implement support for running Qwen models in GGML format using the qwen_cpp library. Create a new QWenModel class that integrates with the existing LLM infrastructure, supporting both chat and text generation modes with streaming capabilities.

*   Create a new QWenModel class in `xinference/model/llm/ggml/qwen.py` extending LLM.
    *   Constructor must accept parameters: `model_uid`, `model_family`, `model_spec`, `quantization`, `model_path`, and an optional `model_config`.
    *   Implement a `_sanitize_generate_config` class method returning a `QWenCppGenerateConfig` with 'stream' defaulting to False if not provided.
    *   Implement a `match` method returning True only when `model_format` is 'ggmlv3', `model_name` contains 'qwen', and `model_ability` contains 'chat'.
    *   Implement a `load` method that imports `qwen_cpp` and raises `ImportError` with installation instructions if unavailable.
    *   Implement a `chat` method returning a `ChatCompletion` dict with choices containing a message with 'role' as 'assistant' and 'content' as generated text when stream is False.
        *   Yield `ChatCompletionChunk` objects when stream is True, with the first chunk containing delta with only 'role' set to 'assistant'.
        *   Subsequent chunks must contain delta with 'content' field containing the generated token.
    *   Implement a `generate` method returning a `Completion` dict with choices containing 'text' field when stream is False.
        *   Yield `CompletionChunk` objects with choices containing 'text' field when stream is True.

*   Add `QWenCppModelConfig` and `QWenCppGenerateConfig` TypedDict classes to `xinference/types.py`.
    *   `QWenCppGenerateConfig` must support `max_tokens`, `top_p`, `temperature`, and `stream` fields.

*   Register `QWenModel` in `LLM_CLASSES` in `xinference/model/llm/__init__.py`.

*   Exclude models with 'qwen' in the name from `LlamaCppModel` and `LlamaCppChatModel` match methods.

*   Ensure the RESTful API handles qwen ggml models similarly to chatglm ggml models, passing `prompt` and `chat_history` without `system_prompt`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.