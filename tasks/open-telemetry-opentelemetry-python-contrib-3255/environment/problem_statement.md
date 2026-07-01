## Description

The VertexAI instrumentation does not capture tool/function calling interactions. When developers use generative AI models with function calling (tool use), the telemetry currently has a significant gap: neither the model's function call requests nor the function results passed back to the model are recorded as observability events.

## Expected Behavior

- When the model responds by requesting one or more function calls, the emitted response event should include structured information about each requested call — its name, a generated identifier, and the arguments (when content capture is enabled).
- When the conversation history includes function results being sent back to the model, dedicated tool message events should be emitted — one per function result — capturing the role, a generated identifier, and the response payload (when content capture is enabled).
- When a conversation turn consists entirely of function results (no user text), the instrumentation should not incorrectly emit an extra user message event for that turn.

## Why This Matters

Without these events, developers using the instrumentation cannot observe which tools were invoked, what inputs the model passed to those tools, or what results were fed back. This makes it impossible to trace and debug multi-turn conversations that involve tool use, defeating the purpose of telemetry for agent-style workloads.
