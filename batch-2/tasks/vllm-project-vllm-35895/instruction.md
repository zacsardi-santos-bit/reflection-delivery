Implement the `MinimaxM2ToolParser` class in `vllm/tool_parsers/minimax_m2_tool_parser.py` to address several issues with the streaming tool call parser for the MiniMax M2 model. Ensure the parser correctly handles content streaming, multiple tool calls, unique identifiers, and token detection.

Requirements:

*   Ensure `MinimaxM2ToolParser` is importable from `vllm.tool_parsers.minimax_m2_tool_parser`.
*   Accept a tokenizer object in the constructor that provides `get_vocab()` returning a dictionary mapping `'<minimax:tool_call>'` and `'</minimax:tool_call>'` to integer token IDs.
*   Implement `extract_tool_calls_streaming` with the following behavior:
    *   If no tool call has started and `delta_text` is non-empty, return a `DeltaMessage` with `content` equal to `delta_text`, leaving `prev_tool_call_arr` empty.
    *   Return `None` if both `delta_text` and `delta_token_ids` are empty.
    *   Detect the start token `'<minimax:tool_call>'` in `delta_text` and stream preceding text as `DeltaMessage` content. Set `is_tool_call_started` to `True` and emit tool calls from completed invoke blocks.
    *   If the start token is in `delta_token_ids` but not `delta_text`, set `is_tool_call_started` to `True` and return `None`.
    *   Emit a `DeltaMessage` with `DeltaToolCall` for each complete `<invoke>` block in `current_text`. Emit all complete invokes in the same delta.
    *   Assign each `DeltaToolCall`:
        *   `index`: sequential 0-based integer.
        *   `type`: "function".
        *   `id`: non-None string starting with "call_".
        *   `function.name`: from the invoke's name attribute.
        *   `function.arguments`: valid JSON string of parameter key-value pairs.
    *   Ensure sequential indices for multiple invokes starting at 0.
    *   After all tool calls, if `delta_token_ids` is non-empty and does not include `'</minimax:tool_call>'`, and `delta_text` is empty with non-empty `prev_tool_call_arr`, return `DeltaMessage(content='')`.
    *   Do not trigger end-of-stream on `'</minimax:tool_call>'` in `delta_token_ids` with empty `delta_text`.
    *   Update `prev_tool_call_arr` with a dict containing 'name' and 'arguments' for each completed invoke.
    *   Reset parser state when detecting the tool call start token with empty `previous_text`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.