Implement a stream interception layer that redacts or blocks sensitive personal data (PII) in real-time streams. Ensure that PII is intercepted before it reaches any consumer, covering all streaming surfaces such as text deltas, reasoning chunks, tool call arguments, tool outputs, and error messages.

*   Implement the `_PIIStreamTransformer` class:
    *   Inherit from `StreamTransformer` (from `langgraph.stream`).
    *   Accept a `rule` (resolved `RedactionRule`) and an optional `lookback` integer (default 128) in the constructor.
    *   Set `required_stream_modes` class attribute to include 'messages' and 'tools'.
    *   Maintain `_buffers` and `_tool_buffers` as separate dict attributes for message and tool data, respectively.
    *   Implement `_redact_value` to redact PII in nested structures, replacing with markers in 'redact' mode or raising `PIIDetectionError` in 'block' mode.
    *   Ensure `_redact_value` returns a new copy of `AIMessage` objects with redacted content.
    *   Implement `process` method to handle various event types, applying lookback-based redaction and raising errors as needed.
    *   Implement `finalize` to clear `_buffers` and `_tool_buffers`.

*   Update `PIIMiddleware`:
    *   Expose a `transformers` attribute.
    *   Ensure `transformers` is an empty tuple when both `apply_to_output=False` and `apply_to_tool_results=False`.
    *   Include a callable factory for `_PIIStreamTransformer` when `apply_to_tool_results=True` or `strategy='block'` and `apply_to_output=True`.

*   Ensure the `process` method in `_PIIStreamTransformer`:
    *   Handles 'messages' channel events with appropriate redaction and error handling.
    *   Manages 'tools' channel events, applying redaction to tool inputs, outputs, and errors.
    *   Clears relevant buffers on message completion and handles legacy payloads correctly.

*   Ensure the `finalize` method:
    *   Clears `_buffers` to an empty dict.

*   Ensure the `PIIMiddleware`:
    *   Registers `_PIIStreamTransformer` correctly in the stream mux transformer list when `apply_to_output=True`.

*   Ensure all streaming events replace PII with '[REDACTED_EMAIL]' or similar markers in 'redact' mode.
*   Raise `PIIDetectionError` immediately when PII is detected in 'block' mode, preventing any PII from reaching the consumer.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.