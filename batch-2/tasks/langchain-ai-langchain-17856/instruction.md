Create a new LangChain partner integration package for the Groq inference service. Implement a chat model class that integrates seamlessly with LangChain's standard chat model interface, supporting synchronous, asynchronous, and streaming invocations. Ensure the model is configurable, secure, and fully serializable, with specific handling for API keys and constructor parameters.

*   Implement the `ChatGroq` class in `libs/partners/groq/langchain_groq/chat_models.py`:
    *   Accept `model` and `model_name` as constructor parameters, both setting the `model_name` attribute.
    *   Handle `api_key` as a secret, stored in `groq_api_key` and not included in string representations or serialized output.
    *   Support serialization and deserialization via `langchain_core` utilities, excluding `groq_api_key` from dumps and loading it from a secrets map using "GROQ_API_KEY".
    *   Accept unknown constructor keyword arguments into `model_kwargs`, emitting a UserWarning if they are not default parameters.
    *   Raise a `ValueError` if:
        *   The same key is provided in both extra kwargs and `model_kwargs`.
        *   A named field (e.g., `temperature`) or "model" appears in `model_kwargs`.
        *   `streaming=True` and `n > 1`.
    *   Provide patchable `client` and `async_client` attributes for synchronous and asynchronous calls, respectively.

*   Implement the `_convert_dict_to_message` function in `libs/partners/groq/langchain_groq/chat_models.py`:
    *   Convert role-based dictionaries to LangChain message types:
        *   "user" -> `HumanMessage`
        *   "assistant" -> `AIMessage`
        *   "system" -> `SystemMessage`
        *   "function" (with "name" and "content") -> `FunctionMessage`

*   Update `libs/partners/groq/langchain_groq/__init__.py`:
    *   Export `ChatGroq` and set `__all__ = ["ChatGroq"]`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.