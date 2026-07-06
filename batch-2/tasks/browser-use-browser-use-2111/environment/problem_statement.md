## Description

The file system module used by browser agents needs to be refactored and cleaned up. The current implementation has accumulated several inconsistencies and design issues that make it harder to use and maintain.

## Problems / Desired Changes

- The file system class does not accept a clear boolean flag to control whether default files are created on initialization — callers need a way to opt out of creating default files.
- There is no method to cleanly destroy the managed file directory and all its contents.
- Size and line count information on file objects are exposed as methods rather than properties, which is inconsistent with how Python idiomatic code usually presents read-only computed values.
- File objects currently lack a clean method to update their content in place, and do not support dedicated disk-sync methods (both synchronous and asynchronous variants).
- The file-reading operation is unnecessarily asynchronous since it reads from an in-memory cache rather than the actual disk.
- When restoring a file system from a previously saved state, unknown file types are silently converted to a default type instead of being safely skipped — this can mask data corruption or version mismatches.
- The state serialization stores the wrong directory path, making round-trip save/restore unreliable.
- File extension parsing does not normalize case, so uppercase extensions like `.TXT` are treated differently from `.txt`.
- There is no easy way to query which file extensions are supported.
- The directory display does not show both the beginning and end of large files — only the start is shown, leaving the user unable to see recent content.

## Expected Behavior

- The constructor should accept both string and path-object base directories and a flag to skip creating default files.
- A method should remove the managed data directory entirely.
- File size and line count should be accessible as properties.
- File objects should support methods to update content in place, write new content to disk, append content to disk, and synchronize content to disk both asynchronously and synchronously.
- The file-reading operation should be synchronous.
- Restoring from a saved state should skip (not substitute) files with unrecognized types.
- State serialization should correctly store the parent directory path, not the data subdirectory path.
- Two exported constants — one for the data directory name and one for the invalid filename error message — should be importable directly from the module.

## Why This Matters

These issues cause subtle bugs when saving and restoring agent state across sessions, and make the API harder to use correctly. Cleaning up the design now will improve reliability and consistency for all callers.
