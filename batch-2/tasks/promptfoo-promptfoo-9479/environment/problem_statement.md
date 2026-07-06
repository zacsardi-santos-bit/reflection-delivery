## Description

When configuring provider tools or callbacks that reference functions defined in external files, users on Windows cannot use Windows-style drive-letter paths. The colon that follows the drive letter is incorrectly treated as the separator between the file path and the function name, which splits the path in the wrong place and causes the file to fail to load. Additionally, the standard local-file URL format that most tools produce on Windows — which includes three slashes after the protocol prefix — does not work because the extra leading slash before the drive letter is not stripped on Windows, producing an invalid path.

## Expected Behavior

- A file reference that uses a Windows drive-letter path followed by a function name should correctly identify the function name at the end, not split on the drive-letter colon.
- A file reference in the triple-slash URL format (the format Windows tools commonly produce) should correctly resolve the drive-letter path on Windows by removing the extra leading slash. On POSIX systems, the same format should preserve the leading slash.
- File paths and directory names that contain colons for legitimate reasons (not as function name separators) should be treated as part of the path and not incorrectly parsed as a function name. A colon should only be treated as a function name separator when the path before that colon resolves to a JavaScript or Python file.
- Function names from Python script files should be supported with the same colon-separator syntax already supported for JavaScript files.

## Why This Matters

Users on Windows who try to configure function callbacks using standard Windows path notation or the standard file URL format currently get cryptic file-not-found errors because the path is being parsed incorrectly. Fixing the path parsing logic to correctly handle drive letters and colons-in-paths would make the feature work reliably across all platforms.
