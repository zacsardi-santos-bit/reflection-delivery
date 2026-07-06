Implement a new response parser that can handle pre-filled chat templates by accepting a prompt prefix and supports incremental streaming. Update existing text generation and multimodal chat pipelines to pass the rendered prompt prefix to the parser, ensuring correct structured output. Ensure the parser is flexible, supporting various model-specific output formats through a template configuration.

*   Create a new module at `transformers/utils/chat_parsing.py` exporting:
    *   `parse_response` function
    *   `ResponseParser` class

*   `parse_response` function:
    *   Accepts a model output string or batch of strings/token-ID sequences, a template spec dict, and an optional `prefix`.
    *   Returns a single dict for a single string input or a list of dicts for a list input.
    *   Template spec must include `defaults`, `start_anchor`, `version`, and `fields`.

*   `ResponseParser` class:
    *   Supports incremental streaming with `feed(chunk)` and `finalize()`.
    *   Emits structured events: `region_open`, `region_chunk`, `region_close`.
    *   Constructor requires a template spec and optional `prefix`.
    *   Exposes `initial_events` and `_output` attributes.

*   Template configuration:
    *   Supports `open`, `close`, `content`, `repeats`, `optional`, `transform`, `transform_each`, and `content_args`.
    *   Validates templates at load time, raising `ValueError` for misconfigurations.

*   Handle specific template patterns for Cohere, Ernie, GPT-OSS, SmolLM, Qwen3, and Gemma4.
*   Ensure template configuration is saveable and reloadable with a tokenizer.
*   Update tokenizer to:
    *   Persist `response_template` through save and reload.
    *   Use `parse_response` for schemas with `version` or `fields`.
    *   Handle token-ID sequences and batch processing.
    *   Provide `get_response_parser(prefix=None)` method.

*   Implement error handling:
    *   Raise `ValueError` for missing `start_anchor`, unsupported `version`, unknown `content`, absent non-optional fields, multiple implicit fields, invalid `transform`, and invalid `open`/`close` lists.
    *   Ensure `start_anchor` truncates correctly and `open`/`close` match correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.