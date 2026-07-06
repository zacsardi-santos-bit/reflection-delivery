## Description

The tool currently uses Unix-specific assumptions about path separators throughout its core logic. It hardcodes the forward slash as the path separator and uses simple string-prefix matching to determine whether one directory is an ancestor of another. This causes two distinct problems:

1. **Cross-platform compatibility**: On Windows, where backslash is the standard path separator, the tool fails to correctly display shortened paths, count directory depth, and handle other path operations.

2. **Incorrect parent-child detection**: The string-prefix approach to parent-child detection has a real correctness bug — a directory and another directory whose name merely starts with the first one's name would incorrectly be classified as parent and child. Being a string prefix does not imply a directory ancestry relationship.

Additionally, the existing function that strips trailing slashes from paths only handles a limited set of cases. Paths with redundant current-directory components or repeated internal separators are not normalized correctly, meaning duplicates may not be recognized as such.

## Expected Behavior

- A new path normalization function should replace the old slash-stripping helper, handling repeated separators, interior current-directory segments, and trailing separator/dot combinations, producing a proper OS-native path string.
- The parent-of detection logic should use proper hierarchical path comparison: a path is only a parent of another if it is a strict ancestor in the directory tree, not merely a string prefix. A path is also not a parent of itself, even if expressed with a trailing slash or a trailing current-directory component.
- Paths should be recognized as equivalent when they differ only in redundant separators or current-directory components.
- Tests that rely on Unix-specific commands unavailable on Windows should be annotated to be skipped when running on that platform.

## Why This Matters

Without these fixes, the tool produces incorrect output on Windows and can misclassify directories as parent-child when they are only string-prefix related. The path normalization fix also prevents false duplicate-path scenarios when users provide paths with trailing slashes or dot segments.
