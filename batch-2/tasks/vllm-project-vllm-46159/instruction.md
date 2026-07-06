I'm working with a streaming parser that handles models with a reasoning/thinking phase followed by regular response content.

*   The CHUNK_SIZES constant (list [1, 2, 3, 5, 11, 23, None]) must be defined and exported from tests/parser/engine/replay_harness.py so it can be imported by other test modules.

*   DelegatingParser must support subclassing where the subclass declares reasoning_parser_cls and tool_parser_cls as class-level attributes pointing to compatible adapter classes; it must accept a tokenizer as its sole constructor argument.

*   Four adapter classes must be importable from vllm/parser/engine/registered_adapters.py: Glm47MoeParserReasoningAdapter, Glm47MoeParserToolAdapter, Qwen3ParserReasoningAdapter, and Qwen3ParserToolAdapter.

*   When streaming tokens through a DelegatingParser subclass configured with GLM4.7-MoE or Qwen3 adapters, the collected output's content field must NOT contain the U+FFFD Unicode replacement character ('\ufffd') even when byte-fallback tokens span the reasoning-to-content transition boundary.

*   The collected output's content field must equal the correctly decoded full text of all content tokens (multi-byte characters must be decoded properly, not replaced with U+FFFD).

*   The collected output's reasoning field must equal the correctly decoded full text of all reasoning tokens.

*   When multiple consecutive byte-fallback tokens appear immediately after the reasoning/content boundary marker, all of them must be decoded correctly — none should produce U+FFFD in the content output.

*   The above U+FFFD-free behavior must hold at every supported chunk size: 1, 2, 3, 5, 11, 23, and None (process all tokens at once).


*   Interface details: Type: Constant
Name: CHUNK_SIZES
Location: tests/parser/engine/replay_harness.py
Signature: CHUNK_SIZES: list = [1, 2, 3, 5, 11, 23, None]
Description: List of chunk sizes used in parametrized streaming replay tests. Moved here from test_delegating_replay.py so it can be shared across multiple test files.

Type: Class
Name: DelegatingParser
Location: vllm/parser/abstract_parser.py
Description: Base parser class that delegates to a reasoning parser and a tool parser. Subclasses declare reasoning_parser_cls and tool_parser_cls as class-level attributes. Instantiated with a single tokenizer argument. Must correctly handle the reasoning-to-content transition without producing U+FFFD in the content output when byte-fallback tokens span the boundary.
Signature: __init__(self, tokenizer) -> None

Type: Class
Name: Glm47MoeParserReasoningAdapter
Location: vllm/parser/engine/registered_adapters.py
Description: Reasoning parser adapter for the GLM4.7-MoE model family. Used as the reasoning_parser_cls in a DelegatingParser subclass.

Type: Class
Name: Glm47MoeParserToolAdapter
Location: vllm/parser/engine/registered_adapters.py
Description: Tool parser adapter for the GLM4.7-MoE model family. Used as the tool_parser_cls in a DelegatingParser subclass.

Type: Class
Name: Qwen3ParserReasoningAdapter
Location: vllm/parser/engine/registered_adapters.py
Description: Reasoning parser adapter for the Qwen3 model family. Used as the reasoning_parser_cls in a DelegatingParser subclass.

Type: Class
Name: Qwen3ParserToolAdapter
Location: vllm/parser/engine/registered_adapters.py
Description: Tool parser adapter for the Qwen3 model family. Used as the tool_parser_cls in a DelegatingParser subclass.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.