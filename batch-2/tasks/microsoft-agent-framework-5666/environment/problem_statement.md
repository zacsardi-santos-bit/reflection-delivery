## Description

When an agent requests user approval before executing a sensitive tool, the hosting server emits an approval request to the caller. On the next turn, the caller sends back their decision (approve or reject). However, the server currently has no way to remember the original request details between turns — so when the approval response arrives, the context of what was actually approved (the specific function and its arguments) is lost.

This is a round-trip problem: approval requests are emitted once and discarded, but approval responses need to reference them. Without persisting the original request data, the agent cannot receive meaningful context about what was approved.

## Expected Behavior

- The server should save approval request data when emitting an approval request item, so it can be retrieved in later turns.
- An in-memory storage option should be available for lightweight, single-session use cases.
- A file-based storage option should be available for scenarios requiring persistence across server restarts.
- Both storage options should reject duplicate saves for the same request ID and raise a lookup error when a requested ID is not found.
- The message conversion functions should become async to support these storage lookups.
- When a caller sends an approval response referencing an unknown request ID, the server should return a server-side error.
- When an approval response arrives for a known request ID, the agent should receive the full context: the approval decision and the original function details that were approved.

## Why This Matters

Without this change, human-in-the-loop approval for tool calls is broken end-to-end: the agent is notified of a decision but has no context about what was decided. This makes the approval mechanism non-functional in multi-turn conversations.
