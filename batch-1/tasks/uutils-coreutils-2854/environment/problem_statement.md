## Description

When listing the contents of a directory that contains a broken symbolic link (one whose target doesn't exist), the ls command prints the same error message **twice** when certain flag combinations are used. Specifically, when inode display, recursive listing, and symlink-dereferencing are all enabled together, the "cannot access" error for the broken link appears duplicated in the error output.

## Expected Behavior

- When listing a directory with a dangling symlink using inode + recursive + dereference-symlinks flags, the error about the inaccessible symlink target should appear **exactly once** in the error output.
- When listing with inode display and symlink-dereferencing flags (but without recursive), a dangling symlink's inode column should show a question mark rather than a real inode number, since the metadata cannot be retrieved.

## Current Behavior

- The same "cannot access" error message is printed twice for the same dangling symlink when using recursive + dereference-symlinks + inode display together.
- The inode for a dangling symlink may not show a question mark as expected.

## Why This Matters

Duplicate error messages are confusing and make it harder to parse the output, especially in scripts. Users expect each problem to be reported once. This also affects how broken symlinks are displayed when inode numbers are requested — showing a question mark is the correct and informative behavior when the inode cannot be determined.
