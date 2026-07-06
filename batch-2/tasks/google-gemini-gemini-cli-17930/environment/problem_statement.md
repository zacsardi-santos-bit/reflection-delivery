## Description

The hooks panel currently works by pushing a message item into the main UI message feed when users run the hooks command. This approach tightly couples hooks display to the message queue and doesn't provide an interactive, scrollable experience.

We should replace this with a proper dialog-style component that is returned by the command action, so that the hooks list is presented in a more polished, self-contained way — separate from the conversation/message feed.

## Expected Behavior

- When the hooks command (or its panel subcommand) is invoked, it should return a dialog result rather than appending to the message queue.
- The dialog should show all configured hooks grouped by the event they are triggered on.
- Each hook entry should show whether it is enabled or disabled, along with the hook's source file, a security warning, and any relevant metadata (such as match conditions, execution order, and timeout).
- If a hook has no explicit name configured, its command string should be used as the display name.
- The dialog should support keyboard navigation: arrow keys for scrolling through long lists, and Escape to close.
- Scroll indicators should appear at the top and bottom of the list when there are more items than fit in the visible area, and disappear when the user reaches the respective boundary.

## Why This Matters

Users with many hooks configured need a readable, navigable view of their hook setup. Embedding the hooks list in the message stream makes it awkward to review; a dedicated closeable dialog is more ergonomic and consistent with how other informational panels are shown in the interface.
