## Description

When a Docusaurus site is located in a directory that is not tracked by any version control system, the tool currently fails in a confusing or cryptic way when it tries to read file history (such as last-updated or creation dates). Users who don't use git — or who have created a fresh directory that hasn't been initialized as a repository — get an unclear error rather than a descriptive explanation of what went wrong.

Additionally, the current version control strategy for reading git file metadata is implemented as a global singleton, which makes it impossible to create independent instances per test or per site. This is inconvenient for testing and for multi-site setups.

## Expected Behavior

- The version control strategy for git should be available as a factory function so that each caller gets a fresh, isolated instance with its own state.
- When the site directory is inside a git repository (including projects using submodules), the strategy should correctly read last-updated and creation timestamps for any tracked file, including files that live inside git submodules.
- When the site directory is **not** inside any git worktree, the strategy should detect this proactively and produce a clear, human-readable error message that identifies both the problem (site is outside a git worktree) and the specific file that could not be read.

## Why This Matters

Without these fixes, users without git, or those debugging configuration in fresh directories, receive no actionable feedback. The singleton design also limits testability and reusability in multi-site or multi-locale scenarios.
