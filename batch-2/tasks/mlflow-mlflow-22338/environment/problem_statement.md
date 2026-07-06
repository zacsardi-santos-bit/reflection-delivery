## Description

We need a new MLflow integration for Claude Code that converts a Claude Code session transcript into a structured MLflow trace. Currently, developers who use Claude Code and MLflow together have no way to observe the detailed flow of an AI coding session — which LLM calls were made, what tools were invoked, what sub-agents ran, and how long everything took — within MLflow's tracing UI.

## Expected Behavior

- A function that reads a Claude Code JSONL transcript file and produces an MLflow trace with a nested span hierarchy:
  - A root agent span capturing the entire conversation, including the initial user prompt and the final assistant response
  - Child LLM spans for each assistant turn, with input message history, output in the expected AI provider format, and the message format attribute set appropriately
  - Child tool spans for each tool invocation, capturing tool name, tool id, inputs, and outputs
  - Nested agent sub-spans for any sub-agent activity, whether reported via inline progress events or loaded from separate transcript files on disk
- Token usage must be tracked on LLM spans, with cache-creation tokens added to input tokens and cache-read tokens excluded
- Trace metadata should include the session id, the current user, the working directory, the AI tool version, and the permission mode
- Request and response previews should be set from the first user message and the final assistant text
- When a tool use is rejected, an exception should be recorded on the tool span
- An empty transcript, a transcript with no real user messages, or a missing file should be handled gracefully without creating any spans
- "Steer" messages injected mid-conversation should be included as user messages in the subsequent LLM span's input history
- All processing should conclude by flushing traces

## Why This Matters

This integration makes Claude Code sessions fully observable within MLflow, allowing teams to inspect, debug, and analyze AI coding agent behavior using the same tooling they use for all their ML experiments and model calls.
