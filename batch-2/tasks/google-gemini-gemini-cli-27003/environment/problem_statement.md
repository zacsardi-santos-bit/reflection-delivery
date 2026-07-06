## Description

When users switch to "auto-edit" approval mode in the Gemini CLI, they expect that all file-modifying operations are automatically approved without interruption. Currently, this mode correctly auto-approves standard file editing operations. However, shell commands that redirect their output directly into files are also effectively file-writing operations, and they should be treated the same way — yet they still require manual confirmation even in auto-edit mode.

This inconsistency forces users to manually approve these commands even when they have explicitly opted into auto-edit mode, defeating the purpose of that mode for such operations.

## Expected Behavior

- When in auto-edit mode, shell commands that write their output to a file via output redirection should be automatically approved alongside other file-editing operations
- Shell commands that do NOT redirect output to a file (i.e., regular command executions) should still require explicit user confirmation even in auto-edit mode

## Why This Matters

Users who set up auto-edit mode to streamline autonomous file modification workflows are repeatedly interrupted by confirmation prompts for shell-with-redirection commands. Aligning these commands with other file edits makes auto-edit mode consistently hands-free for all file-writing operations.
