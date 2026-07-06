## Description

The file copy and move utilities do not correctly handle destination paths that end with a trailing slash. When a user appends a trailing slash to a destination path, it is a common convention to indicate that the destination should be treated as a directory. Several cases are currently broken or inconsistent.

## Expected Behavior

- When copying a file to a destination path that ends with a trailing slash, but that destination does not exist as a directory, the operation should fail with a clear error message indicating the destination is not a directory.
- When recursively copying a directory to a non-existent destination path that ends with a trailing slash (using the no-target-directory option), the operation should succeed and create the destination directory.
- When moving a directory to a destination path ending with a trailing slash and that destination does not exist, the operation should succeed by renaming the source to the destination name.
- When moving a directory to a destination path ending with a trailing slash and that destination already exists as a directory, the operation should succeed by moving the source inside the existing destination directory.

## Why This Matters

Users commonly add a trailing slash to destination paths as a visual reminder that the destination is meant to be a directory. When the tools behave inconsistently or fail silently in these cases, it causes confusion and unexpected results. These fixes make both utilities behave according to standard Unix conventions for trailing slashes in path arguments.
