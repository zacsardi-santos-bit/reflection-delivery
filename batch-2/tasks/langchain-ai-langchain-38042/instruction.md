Update the `_supports_provider_strategy` function to correctly detect whether a given AI model supports native structured output. Ensure that newer Anthropic and Claude models are recognized, while blocking unsupported models with specific qualifiers or types.

*   Ensure `_supports_provider_strategy` returns `True` for:
    *   Newer Claude model names with version 4.5 and above, including variants like `claude-haiku-4-5`, `claude-opus-4-6`, and `claude-sonnet-4-6`.
    *   Claude model family names with version 5 and above, such as `claude-fable-5`.
    *   Anthropic-prefixed Claude model names like `anthropic/claude-sonnet-4-5`.

*   Ensure `_supports_provider_strategy` returns `False` for:
    *   OpenAI model names ending in `-pro` or containing `-oss-`, such as `gpt-5.2-pro` and `gpt-oss-120b`.
    *   Image or video generation models, like `grok-imagine-image`.

*   Implement logic to ignore the `tools` parameter when `_supports_provider_strategy` is called with a bare string model name:
    *   Calls like `_supports_provider_strategy('gpt-5.5')` and `_supports_provider_strategy('gpt-5.5', tools=[...])` should both return `True`.

*   Preserve existing behavior for:
    *   GPT-4.x and GPT-5.x models, including variants like `gpt-4.1-mini` and `gpt-5.4-mini`.
    *   Grok models such as `grok-4.20-0309-reasoning`.
    *   Blocking older Claude models like `claude-opus-4-1`.
    *   Blocking unrelated models like `solar-pro3`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.