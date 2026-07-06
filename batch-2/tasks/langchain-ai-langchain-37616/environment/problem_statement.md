## Description

When an AI agent generates output or invokes tools, sensitive personal data — such as email addresses, credit card numbers, or IP addresses — can appear in the stream before the response finishes. The existing PII middleware only intercepts fully assembled messages in the agent state. This means that anyone consuming the stream in real time sees the raw sensitive data as it arrives, even if the middleware would have caught it in the final state.

## Expected Behavior

- PII should be intercepted and redacted (or blocked) as it flows through the stream, so that no raw sensitive data ever reaches a stream consumer.
- This protection should apply to all streaming surfaces: streamed text deltas, reasoning chunks, tool call arguments (including those that arrive incrementally across multiple chunks), tool outputs (including delta updates), and error messages from failed tool calls.
- When the "redact" mode is active, matched sensitive data should be replaced with placeholder markers in the live stream as well as in finalized snapshots.
- When the "block" mode is active, the stream should be held back entirely and an error raised the moment PII is detected — with no PII leaking through any projection of the stream (text, tool calls, values, or raw events).
- Tool output streaming and tool call argument streaming should be covered by the same protection layer, not just model text output.
- Concurrent agent runs sharing the same transformer instance must not have their buffers cross-contaminated.

## Why This Matters

Without in-flight PII interception, any application that renders streaming output in real time (e.g., a chat interface, a log aggregator, or a monitoring dashboard) would receive raw PII from the model or tools — even when PII middleware is configured. This defeats the purpose of the middleware for real-time use cases and creates a compliance and security gap for production deployments.
