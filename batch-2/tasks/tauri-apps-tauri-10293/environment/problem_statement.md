## Description

There is a bug in how resource paths are resolved when using the map-based resource bundling configuration. When a developer specifies a mapping from a source file to a destination path, the resulting bundled resource ends up at an incorrect location.

For example, when mapping a source file outside the project root to a specific destination path within the bundle, the iterator incorrectly appends the source file's own name to the already-complete destination path, producing a doubly-nested location that doesn't match the intended destination. The developer's specified destination should be used directly without any additional path component being appended.

## Expected Behavior

- When a source file is mapped to a specific destination path, the destination path should be used exactly as provided (no extra filename appended).
- When a source file is mapped to an empty destination, the file should appear at the bundle root using only its filename.
- When a source directory is mapped to a destination, the directory's contents should be placed under that destination preserving subdirectory structure.
- When a glob pattern is mapped to a destination, each matched file should appear directly under the destination using only the filename (not the full matched subpath).
- When directory walking is disabled, directories in the resource list should be silently skipped rather than causing a hard failure.
- Non-existent source paths should be reported as an error through the iterator result type, allowing callers to handle them gracefully.

## Why This Matters

Incorrect resource target paths mean that bundled applications cannot find their expected files at runtime, breaking functionality that depends on bundled resources being at predictable locations. Developers specifying careful destination paths for resources need those paths to be respected as-is.
