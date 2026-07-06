## Description

The session history replay feature in VS Code Copilot, which shows Claude Code conversation history in the chat panel, has two related issues that need to be fixed together.

**1. Renamed subagent tool not recognized**

The external SDK recently renamed the tool used to spawn subagents. The existing code only handles the old tool name, so sessions recorded with the newer SDK version do not display subagent tool calls in the chat history. Both the old and new tool names should be treated identically when rendering session history.

**2. Subagent correlation depends on filesystem workaround**

Currently, the system links a subagent session to the parent tool call that spawned it by reading raw session files from disk, parsing a special field to build an in-memory correlation map, and threading that map through several layers of the session loading pipeline.

This is fragile and unnecessary. The subagent's own conversation data already contains a reference back to the parent tool call that spawned it. The correlation should use this embedded reference directly, which simplifies the pipeline and removes the filesystem dependency entirely.

## Expected Behavior

- Sessions created with either the old or the new tool name for spawning subagents should display correctly in the chat history
- Each subagent session should carry a reference to the parent tool call ID that spawned it; this reference should be used to link the subagent's tool calls under the correct parent entry in the chat UI
- Subagent sessions that have no such reference should be excluded from injection rather than cause errors
- Session loading should not require reading additional files from disk to establish subagent correlation
- Sessions can be loaded without providing a working directory when the workspace context makes the directory unnecessary

## Why This Matters

Without these fixes, sessions from newer SDK versions silently drop subagent tool call history, and the existing workaround adds complexity and disk I/O that can fail independently of the session data itself.
