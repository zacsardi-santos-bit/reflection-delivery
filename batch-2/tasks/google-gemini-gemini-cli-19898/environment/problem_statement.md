## Description

When a user specifies a list of custom ignore file paths for the file discovery service, they can accidentally include directory names instead of file names. For example, a user might list a dependency folder name expecting it to be silently ignored, not realizing the service treats it as an ignore *file*. Currently, the service includes those directory paths in its list of ignore files — which can cause crashes downstream when the code attempts to read a directory as a text file.

## Expected Behavior

- When a custom ignore file path resolves to a directory rather than a file, it should be silently excluded from the list of ignore file paths returned by the service.
- The service should still return all valid file-based ignore paths (e.g., a standard ignore file that exists as a regular file).
- Constructing the service with directory names in the custom ignore file path list — including entries with trailing path separators — should never cause a crash.

## Why This Matters

Users who configure custom ignore paths with directory entries (intentionally or by accident) currently experience application crashes or unexpected behavior. The service should handle this gracefully by skipping non-file entries rather than propagating them, making configuration more forgiving and robust.
