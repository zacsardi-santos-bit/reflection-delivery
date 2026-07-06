Update the `chgrp` utility to improve the error messaging and handling for root protection when using recursive mode. Ensure that the original path provided by the user is reflected in error messages and that all forms of paths resolving to root are correctly identified and handled.

*   Implement error handling for paths resolving to root:
    *   If the target path is the literal root directory '/', ensure the error message is: "chgrp: it is dangerous to operate recursively on '/'", followed by: "chgrp: use --no-preserve-root to override this failsafe".
    *   If the target path is not '/' but resolves to root (e.g., through path traversals, redundant slashes, or symlinks), ensure the error message includes the original path and states it is equivalent to root: "chgrp: it is dangerous to operate recursively on '<original-path>' (same as '/')", followed by: "chgrp: use --no-preserve-root to override this failsafe".
*   Handle symlinks with -HR option:
    *   If a symlink resolves to '/', ensure the error message includes the symlink name and states it is equivalent to root.
*   Detect and handle relative paths when the current working directory is root:
    *   Paths using relative directory navigation shorthands (e.g., '.', '..') must be detected as equivalent to root and trigger the root protection error message.
    *   Paths that resemble directory navigation shorthands but are not (e.g., '...') should not be treated as root and must result in a "cannot access" error indicating the path does not exist.
*   Ensure error messages for root-protection violations are prefixed with 'chgrp: ' and include two lines:
    *   The first line reports the path and its equivalence to '/'.
    *   The second line instructs the user to use --no-preserve-root to override.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.