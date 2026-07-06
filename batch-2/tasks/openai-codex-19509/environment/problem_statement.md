## Description

When MCP tool calls complete, they can return structured metadata alongside their results. This metadata can include useful context such as which target was accessed and whether a server-side user interaction was triggered. Currently, this metadata is discarded entirely — it is never surfaced in the tracing spans that represent the tool call. As a result, operators and developers have no visibility into this information when inspecting distributed traces.

## Expected Behavior

- After an MCP tool call completes, the system should inspect the result's metadata for a designated telemetry section containing span fields.
- If a target identifier is present and is a non-empty string, it should be recorded as a span attribute. If the identifier is excessively long, it should be safely truncated to a defined character limit (256 characters) before recording.
- Truncation must always occur at a valid character boundary so that multi-byte Unicode characters are never split.
- If a flag indicating whether a server-side user interaction was triggered is present and is a boolean, it should be recorded as a span attribute.
- Fields with incorrect types (e.g., a number where a string is expected) should be silently ignored.
- Unknown or unexpected fields in the telemetry section should not be promoted to span attributes.
- If the metadata structure is absent or malformed at any level, no attributes should be recorded and no errors should be raised.

## Why This Matters

Without surfacing this telemetry, tracing infrastructure is blind to meaningful contextual signals embedded in tool call results. Adding this capability allows teams to trace exactly which targets their MCP integrations are accessing and whether server-side flows were triggered, which is critical for debugging and observability.
