## Description

Currently, the build tool can only operate on committed changes — it requires a branch name, commit SHA, or git HEAD to determine which modules to build. This makes local development awkward because developers who are actively working on changes and haven't committed yet have no way to run builds against their modified modules.

We need a way to build based on the **current local state** of the working directory, including:
- Modules that have been modified but not yet committed
- Modules that are brand new and not yet tracked by git

## Expected Behavior

- It should be possible to create a "local" manifest by scanning the working directory and detecting which modules have uncommitted changes or are entirely new.
- The resulting manifest should be clearly marked as a local snapshot (not tied to any git commit).
- When building from this local manifest, only modules with actual local changes should be built by default.
- There should also be an option to include all modules regardless of their change state, for cases where a developer wants a full local build.
- Building from a local manifest should behave just like a regular build: executing build scripts and reporting build stages.

## Why This Matters

This makes the tool much more useful during active development. Developers can iterate quickly on local changes without needing to commit first, and the tool can correctly identify and build only the affected modules — or all modules if a full rebuild is needed.
