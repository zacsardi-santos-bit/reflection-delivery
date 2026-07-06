## Description

The language server formatter only works for files that are inside the currently configured workspace directory. When a user opens a file that lives outside the workspace root — for example, in a parent folder or a sibling directory — the formatter silently fails to produce any edits. This means developers who open files from paths not covered by the workspace get no formatting support at all.

## Expected Behavior

- When the language server is initialized with a specific workspace directory, it should also be able to format files located outside that workspace root.
- Files inside the workspace should continue to format correctly as before.
- Files outside the workspace should receive the same formatting treatment, applying the same language-appropriate transformations.

## Why This Matters

In real editor workflows, developers frequently open files from multiple directories, not all of which fall under a single workspace root. A formatter that silently drops requests for out-of-workspace files creates a confusing experience — the user sees no output and has no indication of why formatting didn't apply. Supporting files outside the workspace is essential for a robust, editor-agnostic language server experience.

## Additional Context

A new operating mode should be introduced that allows the server to dynamically handle files from any location, not just the initialized workspace. This mode should also ensure that file watching remains efficiently scoped to the workspace directory rather than monitoring the entire filesystem.
