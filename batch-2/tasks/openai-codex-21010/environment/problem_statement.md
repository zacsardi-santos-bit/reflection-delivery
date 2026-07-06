## Description

The memories backend does not have a way to report that a requested path does not exist. When callers ask the backend to list entries in a directory that isn't there, or search within a path that doesn't exist, the backend silently returns empty results. Callers have no way to distinguish "the path exists but has no results" from "the path does not exist at all." Likewise, when reading a file at a non-existent path, the wrong error category is returned.

## Expected Behavior

- When listing or searching memories at a path that does not exist on disk, the backend should return a clear "not found" error, not an empty result set.
- When reading a memory file at a path that does not exist on disk, the backend should return a "not found" error rather than a "not a file" error.
- During path resolution, if any component of a scoped path is a symbolically linked directory, the operation should be rejected with an "invalid path" error to prevent symlink-based traversal outside the permitted memory root.
- The "not found" error should be treated as a client error at the server layer, consistent with how malformed paths and cursors are already handled.

## Why This Matters

Without a dedicated "not found" error, clients cannot distinguish missing paths from empty paths, leading to silent failure and confusing debugging experiences. The symlink rejection ensures that the memory root boundary is enforced even when callers construct paths that traverse through symlinked directories.
