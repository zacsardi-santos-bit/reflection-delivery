Fix the `ls` command implementation to handle error messages for dangling symbolic links correctly. Ensure that errors are reported once and inode displays are accurate when certain flags are used.

*   Modify the `ls` command behavior when using inode display, recursive listing, and symlink-dereferencing flags together:
    *   Ensure the error message for an inaccessible symlink target appears exactly once in the error output.
    *   Prevent duplicate error lines for the same dangling symlink.

*   Update the `ls` command behavior when using symlink-dereferencing and inode display flags:
    *   On non-Windows systems:
        *   Ensure the command exits with a non-zero status.
        *   Include 'cannot access' in the error output.
        *   Display '?' in place of the inode number in the standard output, followed by the symlink name (e.g., '? dangle').
    *   On Windows systems:
        *   Ensure the command exits with zero status.
        *   Display the symlink name in the standard output without inode information.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.