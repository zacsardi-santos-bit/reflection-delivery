## Description

The Gemini CLI processes AI requests, tool calls, agent invocations, and user prompts, but currently lacks structured observability instrumentation that follows industry-standard conventions for AI telemetry. Developers cannot trace what data flows into and out of each major operation (LLM calls, tool executions, agent invocations, tool scheduling, and user/system prompts) using standard monitoring tooling.

## Expected Behavior

- A tracing wrapper should instrument key operations throughout the CLI pipeline, creating active spans for each operation
- Each span should capture structured inputs, outputs, and errors using standardized semantic conventions for generative AI
- Default span attributes should be populated automatically, including the operation name, service name and description, and a conversation identifier linked to the current session
- Tracing should be unconditionally active — not gated behind an environment variable
- Streaming operations should be handled correctly by deferring span completion until the stream ends, via a manual span-ending callback
- If errors occur during span finalization, they should be handled gracefully without crashing, and the span should still be properly ended
- The operation classification (LLM call, tool call, user prompt, system prompt, agent call, tool scheduling) should use a well-defined enumeration of operation types

## Why This Matters

Without structured spans around each operation, it is impossible to correlate what inputs triggered what outputs, diagnose failures in specific steps of the pipeline, or monitor the behavior of the CLI using standard observability platforms. This change makes the CLI's internal operations observable in a structured, standardized way.
