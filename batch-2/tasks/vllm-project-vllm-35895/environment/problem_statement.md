## Description

The streaming tool call parser for the MiniMax M2 model has several correctness issues that cause incorrect or incomplete output during inference.

**Current problems:**

1. **Content before tool calls is dropped.** When the model generates a natural-language preamble (e.g., "Let me check the weather for you.") followed by a tool call block, the preamble text is silently discarded instead of being forwarded to the client as content.

2. **Multiple parallel tool calls are not handled.** When the model generates two or more function calls inside a single tool call block, and multiple complete calls arrive in the same streaming chunk, only the first one is emitted — the rest are lost.

3. **Tool call IDs are missing.** Each emitted tool call should have a unique identifier. Currently these identifiers are not being generated correctly, which breaks downstream consumers that rely on them.

4. **Start token detection fails when arriving as a special token.** The tool call block start marker can be delivered by the tokenizer as a special token ID rather than as decoded text. The parser does not handle this case, so it misses the transition into tool-call mode.

5. **End-of-stream signal is triggered by the wrong token.** The tool call close token incorrectly triggers the end-of-stream signal. Only a true end-of-sequence token should cause that signal.

## Expected Behavior

- Text appearing before the first tool call block must be streamed to the client as normal content.
- All complete function calls within a block must be emitted, even when multiple arrive simultaneously in one chunk.
- Each tool call must include a unique identifier with a consistent format.
- The start of a tool call block must be detected whether the start marker arrives as text or as a special token ID.
- The end-of-stream signal must fire only on a genuine end-of-sequence token, not on the tool call close marker.

## Why This Matters

These bugs cause incorrect or incomplete responses when users interact with models that use tool calls. Clients either miss content, miss tool calls entirely, or fail to process responses due to malformed tool call metadata.
