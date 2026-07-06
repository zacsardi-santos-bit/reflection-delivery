Implement incremental streaming of string-typed parameter values in tool calls to improve responsiveness in the LLM serving system. Ensure that string content is forwarded as it is generated, and address related parsing issues such as split parameter tags and leading whitespace.

*   Update the `ParserEngine` class:
    *   Modify the `_safe_arg_prefix` static method to accept an optional `string_keys` parameter of type `set[str] | None`, defaulting to `None`.
        *   When `string_keys` is provided, include string value content in the prefix up to the closing double-quote for keys in `string_keys`.
        *   Handle escaped quotes correctly and return the entire remaining content for unterminated string values.
        *   Ensure the method passes these cases:
            *   `{"k":"value"}` returns `{"k":"value`
            *   `{"k":"unterminated` returns `{"k":"unterminated`
            *   `{"k":"escaped \" quote"}` returns `{"k":"escaped \" quote`
            *   `{"a": "hello", "b": "world"}` returns `{"a": "hello", "b": "world`
    *   Add a new static method `_streamable_string_keys(properties: dict) -> set[str] | None`.
        *   Return a set of keys declared as exclusively string type in the schema.
        *   Return `None` if `properties` is empty or falsy.

*   Update the `ToolCallSlot` class:
    *   Add a new attribute `string_keys` of type `set[str] | None`, initialized to `None`, and include it in `__slots__`.
    *   Populate `string_keys` once when the tool name is resolved and pass it to `_safe_arg_prefix` on each streaming tick.

*   Ensure `_streamable_string_keys` is called exactly once per tool call slot when the tool name is resolved, not per streaming chunk.

*   Update the `qwen3` parser configuration in `vllm/parser/qwen3.py`:
    *   Modify `_qwen3_arg_converter` to strip leading and trailing whitespace from parameter values.
    *   Define `PARAM_START` as `"<parameter="` and `PARAM_END` as `"</parameter>"` as string constants.
    *   Add state transitions for `(TOOL_ARGS, PARAM_START)` and `(TOOL_ARGS, PARAM_END)` that emit `ARG_VALUE_CHUNK` events.

*   Ensure the parser emits multiple argument delta chunks for long string values before the closing parameter tag is received.
    *   The output must form valid partial JSON starting with the opening brace and key, excluding the closing brace/quote until the tag closes.
    *   Buffer split opening parameter tags to prevent partial tag text from appearing in the preceding argument output.
    *   Ensure the final concatenated arguments parse correctly with both preceding and subsequent parameter values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.