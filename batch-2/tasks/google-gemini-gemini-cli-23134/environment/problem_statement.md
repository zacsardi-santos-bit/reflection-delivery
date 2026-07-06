## Description

When the tool searches for project context/instruction files by walking up the directory hierarchy, it currently stops at the configured project trust boundary. If the actual git repository root sits above that trust boundary, any context files placed at the git repository root level are silently ignored and never loaded.

## Expected Behavior

- When the tool walks upward from the trusted project root looking for context files, it should continue up to the nearest git repository root (detected by the presence of a git metadata marker — either as a directory for normal repos, or as a pointer file for submodules and worktrees).
- Context files found in all directories between the trusted root and the git root should be included.
- Files above the git root should never be included.
- When no git repository is detected, the tool should fall back to the existing behavior of stopping at the trusted root.
- Both "just-in-time" context loading and standard environment memory discovery should respect this extended traversal ceiling.
- When multiple nested trusted roots exist within a git repository, traversal should start from the deepest matching root and extend up to the git root, collecting all context files along the way.

## Why This Matters

Users who place instruction or context files at the root of their git repository expect those files to be picked up automatically. The current hard stop at the trust boundary causes these files to be silently dropped, leading to missing context and confusing behavior — especially in monorepos or projects where the working directory is a subdirectory of the git root.
