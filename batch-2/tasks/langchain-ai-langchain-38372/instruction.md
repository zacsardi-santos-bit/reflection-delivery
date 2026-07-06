Implement a feature in the `_construct_responses_api_input` function to handle stateless mode for multi-turn conversations. Ensure that the function processes conversation history correctly based on the `store` parameter, omitting or preserving certain data fields as specified.

*   Update the `_construct_responses_api_input` function in `libs/partners/openai/langchain_openai/chat_models/base.py`:
    *   Add a keyword-only parameter `store` of type `bool | None`, defaulting to `None`.
    *   When `store=False`:
        *   Drop reasoning blocks in `AIMessage` content that do not contain an `encrypted_content` key.
        *   Include reasoning blocks with an `encrypted_content` key as-is, preserving all fields.
        *   Convert text content blocks in `AIMessage` to 'message' items with role 'assistant' and content type 'output_text', omitting the 'id' field.
        *   Keep `function_call` (tool call) items with all fields, including 'id'.
        *   Drop `image_generation_call` blocks with only 'type' and 'id' fields.
        *   Include `image_generation_call` blocks with additional fields, stripping the 'index' field.
    *   When `store=True` or `store=None`:
        *   Convert text content blocks in `AIMessage` to 'message' items, including the 'id' field if available.
        *   Preserve reasoning blocks in `AIMessage` content with all fields intact.

*   Ensure `_construct_responses_api_payload` passes the `store` value from the request payload into `_construct_responses_api_input` to maintain consistent behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.