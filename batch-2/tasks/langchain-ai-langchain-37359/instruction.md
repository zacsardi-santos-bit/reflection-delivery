Implement support for Perplexity's Responses (Agent) API in the LangChain chat integration. Ensure requests are routed correctly based on the presence of built-in tools or specific parameters, and handle incompatible parameters gracefully. Support both streaming and async invocation paths, and ensure Perplexity-specific response extras are properly included.

*   Implement the `_use_responses_api(payload: dict) -> bool` function to:
    *   Return `True` if the payload contains any built-in tool (type not 'function') or keys: 'previous_response_id', 'instructions', 'input', 'include'.
    *   Return `False` for payloads with only function-type tools or no tools.

*   Update `ChatPerplexity` to:
    *   Accept `use_responses_api` parameter (bool or None) to control API path routing.
    *   Implement `_use_responses_api(self, payload: dict) -> bool` to respect `use_responses_api` setting.
    *   Route requests in `invoke`, `ainvoke`, `stream`, and `astream` methods based on `_use_responses_api` result.

*   Implement `_to_responses_payload(self, messages: list[dict], kwargs: dict, user_set_keys: set[str] | None = None) -> dict` to:
    *   Rename 'messages' to 'input' and 'max_tokens' to 'max_output_tokens'.
    *   Drop 'temperature', 'top_p', 'top_k', 'metadata', and 'stop' with appropriate logging.
    *   Raise `ValueError` for 'tool_choice' in `kwargs`.
    *   Merge Perplexity-specific keys into `extra_body`, raising `TypeError` if `extra_body` is not a dict.

*   Implement `_convert_responses_to_chat_result(response: Any) -> ChatResult` to:
    *   Extract content from `response.output_text` or fallback to message items.
    *   Include usage metadata and Perplexity extras in `additional_kwargs`.

*   Implement `_convert_responses_usage(usage: Any) -> dict | None` to:
    *   Return `None` if all token fields are `None`.
    *   Derive `total_tokens` if only `input_tokens` and `output_tokens` are present.

*   Implement `_convert_responses_stream_event_to_chunk(event: Any) -> ChatGenerationChunk | None` to:
    *   Handle 'response.output_text.delta', 'response.completed', and error events appropriately.
    *   Raise `PerplexityResponsesStreamError` for 'response.error' and 'response.failed' events.

*   Define `PerplexityResponsesStreamError` with structured attributes and a detailed string representation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.