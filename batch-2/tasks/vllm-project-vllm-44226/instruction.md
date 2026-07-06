Add an optional flag to the completion and chat completion render endpoints to include character-level start/end offset pairs for each token in the response. Ensure that the offsets align with the original prompt text and are only included when the flag is set. Maintain backward compatibility by returning null offsets when the flag is not set or when conditions do not support offset computation.

*   Update `TokenizeParams` in `vllm/renderers/params.py` to include:
    *   `return_token_offsets: bool = False` as a new field.

*   Modify `CompletionRequest` in `vllm/entrypoints/openai/completion/protocol.py`:
    *   Add `return_token_offsets: bool | None = False` as a new field.
    *   Ensure `build_tok_params(cfg)` forwards `return_token_offsets` to `TokenizeParams`.

*   Modify `ChatCompletionRequest` in `vllm/entrypoints/openai/chat_completion/protocol.py`:
    *   Add `return_token_offsets: bool | None = False` as a new field.
    *   Ensure `build_tok_params(cfg)` forwards `return_token_offsets` to `TokenizeParams`.

*   Update `GenerateRequest` in `vllm/entrypoints/serve/disagg/protocol.py`:
    *   Add `token_offsets: list[tuple[int, int]] | None = None` as a new field.
    *   Ensure this field survives Pydantic `model_dump()` and `model_validate()` operations.

*   Enhance `BaseRenderer` in `vllm/renderers/base.py`:
    *   Implement `_can_produce_offsets(self) -> bool` returning False by default. Override in subclasses to return True if supported.
    *   Update `_tokenize_prompt(self, prompt, params)` to include `prompt_token_offsets` in the result when conditions are met.
    *   Ensure `_process_tokens(self, tokens_prompt)` copies `prompt_token_offsets` to the engine input dict when present.
    *   Apply the same logic to `_tokenize_prompt_async` and `_process_tokens_async`.

*   Update `TokensPrompt` in `vllm/inputs/llm.py` to include:
    *   `prompt_token_offsets: NotRequired[list[tuple[int, int]] | None]`.

*   Update `TokensInput` in `vllm/inputs/engine.py` to include:
    *   `prompt_token_offsets: NotRequired[list[tuple[int, int]] | None]`.

*   Modify the `/v1/completions/render` endpoint:
    *   Include a `token_offsets` field in each response item.
    *   Return null for `token_offsets` when `return_token_offsets` is not set or False.
    *   Provide a list of [start, end] pairs when `return_token_offsets` is True.

*   Modify the `/v1/chat/completions/render` endpoint:
    *   Include a `token_offsets` field in the response dict.
    *   Return null for `token_offsets` when `return_token_offsets` is not set or False.
    *   Provide a list of [start, end] pairs when `return_token_offsets` is True.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.