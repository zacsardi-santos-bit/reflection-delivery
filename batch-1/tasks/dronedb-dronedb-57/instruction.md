Refactor the filesystem path operations into a unified path abstraction class. Implement the `io::Path` class in the `ddb::io` namespace to encapsulate path-related operations, ensuring consistent handling of edge cases and cross-platform compatibility.

*   Define the `io::Path` class in the `ddb::io` namespace inside the `ddb` namespace.
    *   Declare the class in the `src/mio.h` file.
    *   Ensure the class is constructible from an `fs::path` or a string.

*   Implement the `hasChildren` method:
    *   Accept a `std::vector<std::string>` of child path strings.
    *   Return `true` only if every path in the vector is a strict descendant of this path after normalization.
    *   Use `fs::weakly_canonical(fs::absolute(...))` for normalization and strip trailing path separators before comparison.

*   Implement the `depth` method:
    *   Return an integer representing the number of path components above the first entry-point.
    *   Count raw (non-normalized) path components, with specific rules for empty paths, root paths, and paths with `.` components.

*   Implement the `isParentOf` method:
    *   Accept an `fs::path` argument.
    *   Return `true` only when this path is a strict ancestor of the given child path after normalization.
    *   Ensure paths are normalized using `fs::weakly_canonical(fs::absolute(...))`.

*   Implement the `relativeTo` method:
    *   Accept an `fs::path` parent.
    *   Return an `io::Path` with the relative path from the canonical form of the parent to this path.
    *   Return the weakly-canonical absolute form if the parent is the root path.

*   Implement the `generic` method:
    *   Return a `std::string` containing the cross-platform generic string representation of the path.
    *   Use forward slashes on all platforms and strip trailing slashes unless the path is the root `/`.

*   Provide additional methods:
    *   `std::string string() const` to return the native string representation.
    *   `fs::path get() const` to return the underlying `fs::path`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.