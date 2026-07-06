## Description

The ignore-pattern checking system doesn't properly handle directory-specific ignore rules, and there's no way to get a list of all currently ignored paths that actually exist on disk.

## Background

The project uses ignore files (both from version control and from project-specific configuration) to exclude certain paths from processing. These ignore files support patterns that apply specifically to directories (indicated by a trailing slash). However, the current implementation has no way to distinguish whether a given path being checked is a file or a directory, so directory-specific patterns are not evaluated correctly. As a result, a directory that should be excluded by an ignore rule may not be recognized as ignored.

## Problems to Solve

1. **Directory-aware ignore checking**: The methods used to check whether a path is ignored should accept an indication of whether the path is a file or a directory. Directory-specific patterns should only match when the path is actually a directory.

2. **Enumerate ignored paths on disk**: There is currently no way to get a flat list of all paths (files and directories) that are ignored and exist on disk. This capability is needed for IDE integrations, context scoping, and workspace preparation — any use case that needs to know upfront what the active ignore rules exclude.

3. **Directory ignore check**: A dedicated method to check whether a specific directory is ignored would allow callers to efficiently skip traversing into ignored directories.

4. **Path normalization utility**: The logic for normalizing a path relative to a project root (handling absolute vs. relative paths, Windows separators, out-of-root paths, etc.) should be centralized in a reusable utility rather than duplicated across callers.

## Expected Behavior

- Ignore checkers should correctly match directory-specific patterns when told the path is a directory, and correctly skip those patterns for files.
- A new method on the file discovery service should return the list of all ignored paths present on disk, stopping traversal at directories that are already ignored.
- Un-ignore patterns must be respected: explicitly un-ignored files must not appear in the result.
- The enumeration must support filtering by which set of ignore rules to apply.
- Path normalization should handle edge cases: paths outside the root (return nothing), Windows-style separators, the root itself, and sibling directories that share a name prefix with the project root.

## Why This Matters

Without directory-aware ignore checks, directories like `node_modules/` or `dist/` may not be recognized as ignored when their path is passed without a trailing slash. And without the ability to enumerate all ignored paths, consumers of the service cannot efficiently communicate the full exclusion set to other tools.
