## Description

When Claude Code tools finish executing within a chat session, there is currently no telemetry emitted to track the outcome or duration of those tool invocations. This makes it difficult to understand how tools are being used, how often they succeed or fail, and how long they take to run. We need observability into tool execution outcomes as part of the standard message processing pipeline.

## Expected Behavior

- When a tool result is processed (matched to a pending tool call), a telemetry event should be emitted that records:
  - Whether the tool completed successfully, encountered an error, or was cancelled by the user
  - Whether the tool was a built-in tool or came from a third-party server (MCP)
  - The session identifier associated with the invocation
  - How long the tool took to run (when timing information is available)
- When a tool result has no matching pending tool call, no telemetry should be emitted for it

## Why This Matters

Teams need visibility into tool execution within Claude Code sessions to monitor reliability, understand usage patterns, and diagnose issues. Without this telemetry, there is no way to observe whether tools are succeeding or failing in production and how long they are taking to complete.
