## Description

When using multi-agent features, the tool spawns internal background agents to handle sub-tasks. Each of these agents creates its own session file on disk. The problem is that these internal sub-task sessions currently show up in the user-facing session list — the same list a user browses when they want to resume a previous conversation.

This is confusing because the user never started those sessions directly; they are implementation details of tool-use under the hood. A user browsing their history should only see their own top-level conversations, not a long list of internal sub-agent executions they never interacted with directly.

## Expected Behavior

- Sessions created by sub-agents should be distinguishable from user-initiated sessions via a classification field stored in the session record.
- When a session is tagged as a sub-agent session, it must be excluded from the list of sessions available for resumption.
- User-initiated (main) sessions must continue to appear normally in the list.
- Additionally, when a critical internal component fails to initialize, error messages surfaced to the user should display clean, human-readable text — not raw type-prefixed error strings.

## Why This Matters

Users interacting with multi-agent workflows would otherwise see dozens of internal sessions polluting their history, making it difficult to find and resume the conversations they actually care about.
