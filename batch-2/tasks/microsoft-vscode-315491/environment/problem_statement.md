# Session History Missing Tool Calls and Incorrect Announcement Order

## Description

When restoring a completed agent coding session, the history of tool actions the agent performed (such as reading files or running commands) can be silently missing if the underlying event records for those tool invocations are not present. Users see only the assistant's text responses with no trace of the tools that were actually called, making it impossible to understand what the agent did during the session.

Additionally, when a branch context note is injected at the start of a session's history (to indicate which working copy the agent was operating in), it may appear in the wrong position. If the first response in the session includes a tool result, the branch annotation ends up appearing after that tool result rather than as the very first item — resulting in a confusing, out-of-order history.

## Expected Behavior

- When tool invocations are present in the original assistant messages but their lifecycle tracking events are absent, the session history should still reconstruct and display those tool calls, using whatever result information is available.
- Tool calls that failed should reflect their failure status and error details. Tool calls with no recorded outcome should be shown as having completed successfully.
- Intent-signaling tool requests that are not user-visible actions should remain hidden from the displayed history.
- The branch context note, when present, must always be the very first item shown in the restored session history — never displaced by tool results or other content.

## Why This Matters

Developers reviewing or resuming a session need a complete and correctly ordered account of what the agent did. Missing tool calls and a misplaced context note undermine trust in the history view and make it hard to understand the agent's past actions.
