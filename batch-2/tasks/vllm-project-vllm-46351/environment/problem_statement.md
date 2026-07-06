## Description

When a model streams a tool call that contains a long string parameter value, the entire value is buffered until the closing parameter tag arrives before any content is forwarded downstream. For short values this doesn't matter, but for tools that generate substantial text content (summaries, reports, etc.) this means clients see no incremental updates during what could be a multi-second generation window — even though the model is actively producing output.

There are two related issues:

1. **No early streaming of string argument values.** The streaming prefix logic stops at the value boundary for string fields, so string content is never forwarded until the tag closes. For fields that are typed as strings in the tool schema (and therefore cannot be coerced to another type at parse time), it is safe to stream the raw content incrementally.

2. **Split parameter tags can leak into argument output.** If the opening tag for the next parameter arrives split across two streaming chunks (e.g., the beginning of the tag is in one chunk and the rest arrives in the next), the partial tag text can appear in the output of the previous parameter's value.

3. **Leading whitespace in parameter values.** Parameter values that begin with a newline character (e.g. when the value starts on the line after the tag) currently include that leading newline in the final parsed result.

## Expected Behavior

- String-typed parameter values should be streamed incrementally to clients as content is generated, before the closing tag is received.
- The parser should determine which parameter keys are safe to stream early based on their declared schema type.
- The result of that determination should be computed once per tool call, not once per streaming chunk.
- Split opening parameter tags must be buffered and must not appear in the output of any preceding parameter value.
- Parameter values must have leading and trailing whitespace removed.

## Why This Matters

Real-time streaming is a key UX feature of LLM-based tools. When a tool returns a large text response, buffering the entire value before forwarding it eliminates the streaming benefit entirely. Fixing this makes tool-call streaming feel as responsive as regular content streaming.
