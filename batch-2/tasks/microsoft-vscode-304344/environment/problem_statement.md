## Session Restore After Server Restart

## Description

When the agent host server restarts, it loses all in-memory knowledge of sessions that were active during its previous lifetime. However, those sessions may still exist in the underlying agent backend. Currently, if a client tries to subscribe to one of these "orphaned" sessions — sessions visible in the session listing but unknown to the freshly-started server — the server cannot reconstruct any conversation history and fails silently or returns empty state.

## Expected Behavior

- The server should support restoring sessions from a previous lifetime on demand. When a client subscribes to a session that the server has not seen in this process lifetime but that the agent backend recognizes, the server should fetch the session's full message history and rebuild the conversation turn by turn.
- Restored sessions should appear in a ready state with all historical turns populated, including tool calls and response text.
- If a turn was interrupted (a user message was sent but no assistant response was received before the session was lost), that turn should be preserved as cancelled with empty response text, and the next turn should begin normally.
- The restore operation should be idempotent: calling it for a session that is already known to the server should be a no-op.
- Restoring a session should not notify connected clients that a new session was added, since the session was already visible in the session listing before the restore.
- Attempting to restore a session for which no agent can be found should produce a clear error.
- Attempting to restore a session that the agent backend does not recognize should also produce a clear error.

## Why This Matters

Without session restore, any server restart permanently breaks the client's ability to view or continue previous conversations. With this change, clients can seamlessly reconnect to their prior sessions after a server restart, recovering the full conversation context automatically.
