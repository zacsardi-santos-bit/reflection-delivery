## Description

The streaming path utilities in this library provide wrappers around common path operations (joining, opening, getting the stem or suffix) that work uniformly for both local file paths and remote URLs. However, there is no equivalent for glob-style pattern matching. This means dataset scripts cannot enumerate files by pattern when running in streaming mode — for example, listing all parquet files under date-partitioned directories, or finding all files matching a name pattern within a remote location.

## Expected Behavior

- There should be a glob utility that accepts a path and a pattern string, returning all entries in that directory whose names match the pattern, as path representations.
- There should be a recursive glob utility that accepts a path and a pattern string, returning all matching entries anywhere in the directory tree, as path representations.
- Both utilities should work the same way for local paths and remote filesystem URLs.

## Why This Matters

Without glob and recursive glob support in the streaming path utilities, dataset scripts that need to discover files dynamically (for example, to handle partitioned data organized in dated directories) either cannot work in streaming mode or must use separate code paths for local and remote access. Adding these utilities closes the gap and makes streaming mode a complete replacement for file-based access patterns.
