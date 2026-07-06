## Description

The tool used to finalize the planning phase currently requires the model to provide a full relative path to the plan file, including the plans subdirectory prefix (e.g., "plans/my-feature.md"). This is unnecessarily verbose and error-prone — the model has to know and reproduce the directory structure prefix every time. Since all plan files live in a single designated directory, the tool should accept just the filename (e.g., "my-feature.md") and resolve the full path internally.

There is also an issue with how the tool operates during the planning phase itself: when the tool is in plan mode, file edit operations currently write to the normal workspace location rather than the plans directory. Edits made during planning should be redirected to the plans directory to keep all planning-phase changes scoped correctly.

## Expected Behavior

- The tool that finalizes plan mode should accept a plain filename (not a full path with directory prefix) as its parameter.
- The parameter should be renamed to make it clear that only a filename is expected, not a directory-prefixed path.
- Validation should reject empty or missing filenames with a clear error message.
- Validation should reject filenames that, when resolved, point outside the plans directory (e.g., via symbolic links), with an error message that includes both the resolved path and the designated plans directory for easier debugging.
- The underlying path validation utility should no longer need a root/target directory as a third argument — path safety can be ensured by resolving against the plans directory alone.
- When the system is operating in plan mode, file edit operations should be redirected to write into the plans directory rather than the workspace, using the basename of the requested file path.
- The configuration interface should expose a way to query whether the system is currently in plan mode.

## Why This Matters

Requiring the model to construct a full directory-prefixed path when only a filename is needed adds unnecessary complexity and a common source of errors. Simplifying to a filename-only interface makes the tool easier to use correctly. The plan mode redirect for edits ensures that changes made during planning stay within the plan context and don't accidentally modify workspace files.
