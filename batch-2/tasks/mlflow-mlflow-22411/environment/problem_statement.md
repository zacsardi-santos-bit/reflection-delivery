## Description

We need to add tracing support for conversations recorded by the Qwen coding agent integration. When a coding agent session completes, it produces a JSONL transcript file capturing every user message, model response, and tool interaction. Currently there is no way to convert these transcripts into MLflow traces, so the sessions remain invisible to our observability tooling.

## Expected Behavior

- A transcript processor should read a JSONL conversation transcript and produce a structured MLflow trace with one root agent span per conversation, one model-call span per assistant response, and one tool span per tool invocation.
- The root span should carry the raw user prompt as input and the final assistant reply as output.
- Model-call spans should receive conversation history in a standard chat-completion request format and output the assistant's reply in the standard response format, excluding any internal reasoning ("thought") content from the rendered output.
- Tool spans should be labeled with the tool name and call identifier, carry the tool's arguments as inputs and the tool output as their result, and be marked with an error status when the tool call was cancelled by the user.
- Token usage should be attributed accurately: each model-call span gets the counts from its individual API call, while the overall conversation span gets the last prompt-token count (to avoid double-counting cumulative context) plus the sum of all output token counts.
- All span timestamps should reflect actual wall-clock intervals (start and end as nanoseconds) based on the timestamps in the transcript.
- When no transcript path is provided, the processor should skip trace creation entirely.
- A companion set of utility functions should handle low-level transcript parsing: reading JSONL files into typed records, slicing the last conversation turn, extracting text (with control over whether internal reasoning is included), parsing function calls, building tool-result lookup tables, normalizing tool output displays, and mapping usage metadata to token counts.

## Why This Matters

Without this tracing support, developers using the Qwen coding agent integration have no visibility into session-level behavior, token costs, or tool usage patterns through the MLflow UI. This change bridges that gap by turning raw transcript files into first-class MLflow traces.
