Implement support for the Nemotron V3 model family in the vLLM engine-based parser infrastructure. Ensure that the parser handles reasoning/content swaps and tool-call parsing correctly. Migrate the existing standalone reasoning adapter to the engine-based adapter system.

Requirements:

*   Implement `NemotronV3Parser` as a subclass of `ParserEngine`.
    *   Export it from `vllm/parser/nemotron_v3.py` and `vllm/parser/engine/registered_adapters.py`.
    *   Set `parser_engine_config.name` to "nemotron_v3".
    *   Include "TOOL_END" in `parser_engine_config.token_id_terminals`.
    *   Include "THINK_END" (value: "</think>") and "TOOL_START" (value: "<tool_call>") in `parser_engine_config.terminals`.

*   Implement `extract_reasoning(text, request)` method.
    *   Return `(None, text)` when `enable_thinking=False` or `force_nonempty_content=True` and no `</think>` is found.
    *   Return `(whitespace, before_think)` if `</think>` is present and content after is whitespace-only.
    *   Return `(before_think, after_think)` if content after `</think>` is non-empty and non-whitespace.
    *   Return `(text, None)` if `request` is `None`, `chat_template_kwargs` is absent, or `enable_thinking=True`.

*   Implement `extract_tool_calls(text, request)` method.
    *   Parse `<tool_call>...<function=NAME>...<parameter=KEY>VALUE</parameter>...</function>...</tool_call>` XML.
    *   Return `tools_called=True` with a populated `tool_calls` list for detected tool calls.
    *   Support parallel tool calls.
    *   Return `tools_called=False` and `content=None` if no tool calls are present.

*   Implement `parse_delta(delta_text, delta_token_ids, request, prompt_token_ids=None, finished=False)` method.
    *   Use token-ID filtering to detect tool calls.
    *   Resolve deferred scanner state when `finished=True`.

*   Ensure `_tool_slots` is accessible post `parse_delta`, containing objects with `.name` attributes.

*   Implement `NemotronV3ParserReasoningAdapter` as a subclass of `ParserEngineReasoningAdapter`.
    *   Export it from `vllm/parser/engine/registered_adapters.py`.
    *   Set `_parser_engine_cls` to `NemotronV3Parser`.
    *   Ensure the tokenizer supports `decode(token_ids: list[int]) -> str`.

*   Implement `NemotronV3ParserToolAdapter` as a subclass of `ParserEngineToolAdapter`.
    *   Export it from `vllm/parser/engine/registered_adapters.py`.
    *   Set `_parser_engine_cls` to the same engine class as `NemotronV3ParserReasoningAdapter`.

*   Update test infrastructure:
    *   Add `_build_nemotron_v3` function in `tests/parser/engine/trace_builder.py`.
    *   Register `_BUILDERS["nemotron_v3"] = _build_nemotron_v3`.
    *   Ensure `adjust_request` sets `skip_special_tokens=False`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.