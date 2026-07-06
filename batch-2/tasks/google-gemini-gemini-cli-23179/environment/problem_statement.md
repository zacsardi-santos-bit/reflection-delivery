## Description

When an AI agent requests user permission before running a tool, the permission dialog currently uses the same full description string for both the title and any contextual details. For shell commands this means the title contains the command together with the working directory, a description annotation, and a background flag all crammed together. For MCP tools the entire parameter payload appears as the title. The UI cannot easily separate the short identifier (the command name) from its context.

This makes permission dialogs harder to read and prevents the UI from routing information to the right places. A concise title (e.g. just the shell command) should appear in the title slot, while richer context (directory, description, background status, or the full parameter payload) should be delivered as a separate message so the UI can present it as secondary information.

## Expected Behavior

- Tools should expose a short display title that contains only the most human-relevant identifier — for shell tools, just the command; for MCP tools, the command argument when available, otherwise the tool's display name.
- Tools should also expose a separate explanation containing the contextual metadata — for shell tools, the directory, description, and background status; for MCP tools, the serialized parameters (with a graceful truncation message for large payloads).
- The protocol layer that requests permissions should use the short title for the permission dialog heading and deliver the explanation as a distinct thought update, keeping the two concerns separated.

## Why This Matters

Cleaner separation of title vs. context lets UIs display a readable, scannable permission prompt while still making the full details available in the appropriate place, improving the overall user experience when tools are executed.
