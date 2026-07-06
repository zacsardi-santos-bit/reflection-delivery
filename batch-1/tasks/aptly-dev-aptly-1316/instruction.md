Implement a fix for the directory accessibility check in the `DirIsAccessible` function to correctly handle directories with fully restricted permissions. Ensure the function returns an appropriate error message when a directory is inaccessible due to permission settings.

*   Update the `DirIsAccessible` function located in `utils/utils.go` to handle cases where a directory exists but is inaccessible due to permission restrictions.
    *   The function signature is `DirIsAccessible(filename string) error`.
    *   Ensure the function returns a non-nil error if the directory has permissions set to 0000 or otherwise denies access.
    *   Format the error message as: "'<path>' is inaccessible, check access rights", replacing `<path>` with the actual directory path provided to the function.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.