Implement a solution to correctly classify generated tokens in a multi-turn conversation when a prompt ends inside an open reasoning channel. Ensure the parser initializes in the correct state to prevent internal reasoning from appearing in user-visible content.

*   Update `Gemma4Parser` in `vllm/parser/gemma4.py`:
    *   Implement `adjust_initial_state_from_prompt(prompt_token_ids: Sequence[int]) -> None`.
    *   Pre-initialize the parser to `REASONING` state if `is_reasoning_end(prompt_token_ids)` returns `False`.
    *   Set `self._streaming_initialized = True` to avoid overwriting the state with a default initialization.

*   Modify `ParserEngine` in `vllm/parser/engine/parser_engine.py`:
    *   Implement a no-op `adjust_initial_state_from_prompt(prompt_token_ids: Sequence[int]) -> None`.
    *   Ensure this method is called once per streaming session on the first call with `prompt_token_ids`.
    *   Use a `_prompt_streaming_prepared` flag to prevent multiple calls and reset it in `_reset`.

*   Update `ReasoningParser` in `vllm/reasoning/abs_reasoning_parsers.py`:
    *   Define a no-op `adjust_initial_state_from_prompt(prompt_token_ids: Sequence[int]) -> None` for subclass overrides.

*   Modify `ParserEngineReasoningAdapter` in `vllm/parser/engine/adapters.py`:
    *   Implement `adjust_initial_state_from_prompt(prompt_token_ids: Sequence[int]) -> None` to delegate to the underlying parser engine.

*   Update `DelegatingParser` in `vllm/parser/abstract_parser.py`:
    *   Call `adjust_initial_state_from_prompt(prompt_token_ids)` when reasoning has not ended in the prompt.

*   Ensure tokens generated before the reasoning close marker are classified as reasoning output:
    *   They should appear only in the reasoning field, not in the content field.

*   Ensure tokens generated after the reasoning close marker are classified as content output:
    *   They should appear in the content field.

*   Handle redundant reasoning channel open tokens:
    *   Silently discard these tokens when the parser is pre-initialized to `REASONING`.

*   Ensure normal behavior is preserved when the prompt does not end inside an open reasoning channel.

*   Add a no-op transition for `(ParserState.REASONING, "THINK_START")` in `vllm/parser/gemma4.py`:
    *   Map it to `Transition(ParserState.REASONING, ())` to prevent leaking tokens.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.