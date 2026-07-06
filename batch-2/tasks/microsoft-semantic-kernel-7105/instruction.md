Implement enhancements to the AI framework's function call and result content types to improve flexibility and usability. Ensure that objects can be constructed with separate plugin and function names, handle dictionary arguments natively, and improve merging behavior for streaming content.

*   Update `FunctionCallContent` class:
    *   Accept `function_name` and `plugin_name` as separate constructor parameters.
    *   Derive `name` as "plugin_name-function_name" when initialized with separate names.
    *   Expose `function_name` and `plugin_name` as Pydantic model fields.
    *   Support dictionary arguments in addition to JSON strings.
    *   Implement `parse_arguments()` to return the dictionary directly if arguments are a dict.
    *   Handle addition of two instances:
        *   Merge dictionary arguments or raise `ContentAdditionException` for mixed types.
        *   Raise `ContentAdditionException` for mismatched `id` or `index`.
        *   Handle special cases for combining arguments, such as empty strings and JSON objects.

*   Update `FunctionResultContent` class:
    *   Accept `function_name` and `plugin_name` as separate constructor parameters.
    *   Derive `name` as "plugin_name-function_name" when initialized with separate names.
    *   Expose `function_name` and `plugin_name` as Pydantic model fields.
    *   Implement `to_chat_message_content()` to convert results to chat messages:
        *   Return `TextContent` when `unwrap=True`.
        *   Return `FunctionResultContent` when `unwrap=False`.

*   Define `ContentAdditionException`:
    *   Located in `semantic_kernel/exceptions/content_exceptions.py`.
    *   Importable from `semantic_kernel.exceptions`.
    *   Raised for incompatible `FunctionCallContent` additions.

*   Update `StreamingChatMessageContent` class:
    *   Preserve both items when `StreamingTextContent` items differ in `choice_index`, `ai_model_id`, or `encoding`.
    *   Maintain existing behavior to raise `ContentAdditionException` for differing outer message properties.

*   Ensure kernel's error log uses the format: "Received invalid arguments for function {name}: {error_message}. Trying tool call again."

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.