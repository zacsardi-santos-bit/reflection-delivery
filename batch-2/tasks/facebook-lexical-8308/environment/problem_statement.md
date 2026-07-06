## Description

When a nested editor dispatches a command, that command is currently siloed within the child editor and never reaches the parent editor's command handlers. This is a fundamental gap in the nested editor hierarchy: developers who register command listeners on a parent editor cannot receive events that originate from a child editor.

## Expected Behavior

- When a nested child editor dispatches a command, it should propagate to the parent editor after the child's own handlers have finished.
- If the parent editor is idle (not in the middle of an update) when the child dispatches the command, propagation to the parent should happen synchronously — both the child and parent listeners should finish before the dispatch call returns.
- If the parent editor is actively updating when the child dispatches the command, the child's handlers should still run immediately, but propagation to the parent should be deferred until the parent's current update finishes.
- When a parent handler runs for a command that originated from a child editor, the parent handler should receive the child editor as the originating editor, while the active editor context should reflect the parent editor.

## Why This Matters

Without command delegation through the editor hierarchy, building features that depend on coordinated command handling between parent and child editors is impossible. For example, keyboard shortcuts, undo/redo integration, or other cross-editor behaviors silently break when commands are dispatched from a nested editor. Fixing this allows nested editor setups to work naturally as a proper hierarchy.
