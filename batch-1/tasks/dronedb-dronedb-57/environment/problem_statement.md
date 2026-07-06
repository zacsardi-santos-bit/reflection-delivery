## Description

The codebase currently has a collection of standalone utility functions for common filesystem path operations — checking whether a set of paths are all children of a given parent, computing path depth, determining parent-child relationships, and computing relative paths. These are spread across a general-purpose utility module as free functions, making them harder to discover and use consistently.

We should refactor these into a unified path abstraction so that path-related operations are grouped together in a single, coherent type. This would allow callers to construct a path object and invoke methods on it rather than passing raw strings to disconnected functions.

## Expected Behavior

- A path type should provide a method to check whether all of a given list of paths are strict descendants of the current path (i.e., genuinely "under" it, not equal to it or outside it).
- A method to return the depth of a path (number of levels below root or entry point).
- A method to check whether this path is strictly a parent (ancestor) of another path.
- A method to compute the path relative to a given parent path, returning the original normalized absolute path when no relative form is possible (e.g., when the parent is the root).
- A method to return a cross-platform string form of the path.
- All these methods must correctly handle edge cases: paths with current-directory or parent-directory components, trailing slashes, and platform-specific path separators.

## Why This Matters

Path manipulation is used throughout the project when indexing and traversing the drone data file system. Having a clean, well-tested abstraction ensures consistent behavior and makes it easy to add new path operations in the future without duplicating normalization logic.
