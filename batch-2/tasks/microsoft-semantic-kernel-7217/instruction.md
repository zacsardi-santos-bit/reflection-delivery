Implement support for automatic function/tool calling, correct message formatting bugs, and ensure consistent method signatures for embedding generation in the Azure AI Inference connector. Validate prerequisites and constraints for function invocation, format messages correctly, and adjust the embedding method to accept settings as a positional argument.

*   Implement automatic function/tool calling:
    *   Ensure `get_chat_message_contents()` and `get_streaming_chat_message_contents()` validate the presence of a Kernel instance when `function_choice_behavior` is set and auto invocation is enabled.
    *   Raise `ServiceInvalidExecutionSettingsError` if the `kernel` keyword argument is missing or if the `extra_parameters` dict includes 'n' with a value greater than 1.
    *   For `get_chat_message_contents()`, ensure the service loops through invocations up to the configured limit and reports the final result with `finish_reason` equal to `FinishReason.TOOL_CALLS`.
    *   For streaming, ensure the underlying complete API is called exactly once when `maximum_auto_invoke_attempts=1`.

*   Correct message formatting:
    *   Update the assistant message formatter to return an `AssistantMessage` with content as a string and populate `tool_calls` from `FunctionCallContent` items.
    *   Ensure the tool message formatter raises a `ValueError` if the first item is not a `FunctionResultContent`.
    *   Update the `MESSAGE_CONVERTERS` dict to include entries for all `AuthorRole` enum values.
    *   Ensure the system message converter returns a `SystemMessage` with content equal to the input message's content.
    *   Ensure the user message converter returns a `UserMessage` with content as a string or a list of `TextContentItem` and `ImageContentItem` objects, skipping unsupported items.

*   Ensure consistent method signatures:
    *   Modify `generate_embeddings()` in `AzureAIInferenceTextEmbedding` to accept settings as a positional-or-keyword parameter.
    *   Ensure the `kwargs` dict passed to the internal embed call is empty and does not contain the settings object.

*   Update `get_prompt_execution_settings_class()` to return `AzureAIInferenceChatPromptExecutionSettings`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.