## Description

We want to add MLflow tracing support for a coding assistant tool so that each completed assistant turn is automatically recorded as a structured trace. Currently, when the assistant runs — fielding a user request, reasoning through the problem, calling shell tools, and producing a final response — none of that activity is captured in MLflow. This makes it impossible to observe, debug, or analyze agent behavior after the fact.

## Expected Behavior

- Each completed turn should produce a top-level agent span that records the user's original request as its input and the assistant's final response as its output.
- For each round of model inference within a turn, a nested language-model span should be created that captures the full conversation history leading up to that inference and the model's response.
- Each tool invocation (e.g., a shell command execution) should produce its own child span with the tool name, call identifier, parsed arguments, and the tool's output.
- Tool spans should reflect whether the tool call succeeded or failed, including the exit code when there is a failure.
- Span timestamps should accurately bracket the work done — the root span covers the entire turn, and each child span's start and end times correspond to the actual timestamps from the transcript.
- When no user prompt is present in a turn, no spans should be created and nothing should be flushed.
- Trace metadata should record the session and user identifiers.

## Why This Matters

Without this tracing, developers using the coding assistant have no visibility into multi-step agentic behavior. Adding these traces lets teams audit what the model did, how long each step took, which tool calls failed, and what the full conversation context looked like at each inference — all through the standard MLflow tracing interface.
