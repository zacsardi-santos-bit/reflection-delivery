Implement a feature to suppress internal reasoning content in non-streaming chat completion responses. Ensure that the server accepts requests with reasoning suppression enabled and returns only the visible answer text without any reasoning content or related metadata.

*   Update the server to accept non-streaming chat completion requests with `include_reasoning` set to false:
    *   Ensure the server returns HTTP 200 OK for such requests.
    *   Remove any validation errors that previously rejected these requests.

*   Modify the response structure for non-streaming requests when `include_reasoning` is false:
    *   Ensure `choices[0].message.content` contains only the visible answer text.
    *   Exclude the `reasoning` field from `choices[0].message`.
    *   Omit `logprobs` and `token_ids` fields from `choices[0]` if reasoning tokens were generated, even if `logprobs: true` and `return_token_ids: true` were requested.
    *   Retain the `prompt_token_ids` field in the response.

*   Update the `PreparedRequest` struct in `rust/src/server/src/routes/openai/chat_completions/convert.rs`:
    *   Add a `pub include_reasoning: bool` field.
    *   Populate this field from the incoming `ChatCompletionRequest`.

*   Modify the `prepare_chat_request` function:
    *   Ensure it sets the `include_reasoning` field in `PreparedRequest` based on the incoming request.

*   Adjust the `validate_request_compat` function in `rust/src/server/src/routes/openai/chat_completions/validate.rs`:
    *   Remove the guard that rejects requests where `include_reasoning` is false.

*   Update the `chat_completion_chunk_stream` function in `rust/src/server/src/routes/openai/chat_completions.rs`:
    *   Add an `include_reasoning: bool` parameter after `requested_logprobs`.
    *   Suppress reasoning block deltas and related per-token metadata when `include_reasoning` is false.

*   Modify the `collect_chat_completion` function in `rust/src/server/src/routes/openai/chat_completions.rs`:
    *   Add an `include_reasoning: bool` parameter after `include_prompt_logprobs`.
    *   Set the reasoning field to None and omit output metadata when `include_reasoning` is false.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.