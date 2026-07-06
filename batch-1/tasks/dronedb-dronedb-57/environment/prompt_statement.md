I'm working on refactoring how filesystem paths are handled in a C++ codebase. Right now there are several standalone utility functions for common path operations — things like checking if a group of paths are all contained within a given parent directory, computing how deep a path is, checking parent-child relationships between two paths, and computing a path relative to a given base. These are scattered as free functions in a general utility file.

I'd like to introduce a proper path abstraction — a class that wraps a filesystem path and exposes these operations as methods. The class should live in an I/O-focused namespace and be declared in a new header file. It needs to handle tricky edge cases correctly: paths that contain current-directory or parent-directory components, paths with trailing slashes, and cross-platform differences between Windows and Unix-style paths.

Specifically, the new type needs to support:
- Checking whether every path in a given list is a strict descendant of the current path (not equal, not outside it — only genuinely nested under it).
- Returning the depth of the path as an integer, where root-level paths have depth zero.
- Checking whether this path is strictly a parent of another path (handling normalization so that paths that resolve to the same location through parent-directory references are not counted as children).
- Returning the path expressed relative to a given base, and when no relative form exists (e.g., the base is the root), returning the normalized absolute path as-is.
- Returning a generic cross-platform string representation of the path.

The old standalone utility functions for path operations can be removed once the new type covers the same ground.
