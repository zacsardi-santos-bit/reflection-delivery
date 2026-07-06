## Description

The agent executor currently lacks support for streaming and async execution. Developers building async applications or wanting real-time visibility into agent steps have no clean way to iterate over agent steps as they happen — either synchronously or asynchronously. Additionally, the existing iterator API for stepping through agent execution has a confusing parameter that developers must set to opt into async behavior, which should instead be handled automatically.

## Expected Behavior

- The agent executor should support a streaming interface that emits each agent step as it occurs. Each event should be a dictionary indicating either an action being taken, an observation received from a tool, or the final answer.
- There should be an async variant of streaming so that agents can be used cleanly in async applications via asynchronous iteration.
- The agent should support async versions of its run/call methods for non-streaming async usage.
- A utility function should allow combining all streamed chunks into a single aggregated result by merging lists and scalars appropriately.
- The iterator API should be simplified: the parameter for enabling async iteration should be removed in favor of automatic detection, and a new option should be added to include run-tracking metadata in the final output.
- The final output from an iterator run, when run info is requested, should include a unique run identifier that callers can use for tracing and linking execution runs.

## Why This Matters

Without async streaming support, developers are forced to block on agent execution or resort to workarounds, making it difficult to integrate agents into real-time or async-first applications. The streaming API makes agents composable with other streaming workflows, and the run ID support makes it possible to trace and debug agent runs end-to-end.
