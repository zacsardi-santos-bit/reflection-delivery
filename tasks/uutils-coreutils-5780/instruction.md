Fix the handling of trailing slashes in destination paths for the `cp` and `mv` commands to ensure consistent behavior. Implement the specified behaviors for cases where the destination path ends with a trailing slash and may or may not exist as a directory.

*   Update the `cp` command:
    *   Ensure that when copying a file to a destination path ending with a trailing slash, if the destination does not exist as a directory, the operation fails with the error message: `cp: '<dest>' is not a directory`.
    *   Ensure that when recursively copying a directory with the `-r -T` flags to a non-existent destination path ending with a trailing slash, the operation succeeds and creates the destination directory.

*   Update the `mv` command:
    *   Ensure that when moving a directory to a destination path ending with a trailing slash, if the destination does not exist, the operation succeeds by renaming the source directory to the destination name without the trailing slash.
    *   Ensure that when moving a directory to a destination path ending with a trailing slash, if the destination already exists as a directory, the operation succeeds by moving the source directory inside the existing destination directory.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.