## Description

When the AI assistant reads, writes, edits, or lists files in a project, it should automatically discover and surface any project-specific guidelines or conventions that exist in subdirectories relevant to those files. Currently, this kind of context is only loaded once at the start of a session, meaning the AI may miss important local conventions for code in subdirectories it visits later. This "just-in-time" context should be appended to the tool's output so the AI can immediately apply those conventions.

Additionally, when a chat session is reset, the context manager should be refreshed to clear any previously loaded paths, preventing stale context from carrying over into the new session.

## Expected Behavior

- When a file operation tool (read, write, edit, list directory, or read many files) executes, it should attempt to discover any new project context relevant to the accessed path.
- If new context is discovered, it should be appended to the tool's output with clearly marked section delimiters.
- If context discovery is disabled, unavailable, or fails, the tool should complete normally without any context appended.
- When the chat is reset, any context manager tracking loaded paths should be refreshed so the new session starts clean.
- Chat reset must succeed gracefully even when no context manager is configured.

## Why This Matters

Without this feature, the AI assistant may apply outdated or incorrect conventions when working in subdirectories it has not previously explored during a session. Automatically surfacing subdirectory-level context as files are accessed ensures the AI always has the most relevant project guidelines available.
