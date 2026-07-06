Implement the ability for the `convert_to_messages` function to recognize and process serialized message envelopes in LangChain core. Ensure the function can reconstruct the correct message types from these envelopes and handle errors appropriately.

*   Update the `convert_to_messages` function in `libs/core/langchain_core/messages/utils.py` to:
    *   Accept dicts in the Serializable constructor-envelope format: `{"lc": 1, "type": "constructor", "id": ["langchain_core", "messages", "<ClassName>"], "kwargs": {...}}`.
    *   Convert these envelopes to the appropriate message type using the `kwargs` provided.
    *   Maintain compatibility with existing dict formats that include a 'role' or 'type' key.

*   Modify the `_convert_to_message` function in the same file to:
    *   Detect the full envelope signature: `lc` equals integer 1, `type` equals "constructor", `id` is a list, and `kwargs` is a dict.
    *   Extract the class name from `id[-1]` and map it to the corresponding message type.
    *   Reconstruct messages for supported class names:
        *   "HumanMessage": Set all `kwargs` fields, including `content` and `id`.
        *   "AIMessage": Preserve `tool_calls` lists exactly.
        *   "SystemMessage": Set the `content` field.
        *   "ToolMessage": Set `content`, `tool_call_id`, and `status` fields. If `additional_kwargs` contains 'artifact', set the `artifact` attribute.
        *   "FunctionMessage": Set `content` and `name` fields.
        *   Chunk variants (e.g., "HumanMessageChunk") should convert to their parent message type.
    *   Raise a `ValueError` with the message 'MESSAGE_COERCION_FAILURE' for unknown or unsupported class names.
    *   Ensure that partially-formed dicts that resemble the envelope format but lack required fields fall through to the existing error path.

*   Ensure that existing plain dict inputs with a 'role' or 'type' field continue to function as before without interference from the new envelope handling.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.