## Description

The zip file extraction used for runtime environment packaging is vulnerable to path traversal attacks (also known as "zip slip"). A malicious or corrupted zip archive can contain entries with relative path traversal sequences or absolute paths that, when extracted, cause files to be written outside the intended destination directory — potentially overwriting arbitrary files on the filesystem, including those in parent or sibling directories.

## Expected Behavior

- When extracting a zip archive, any entry whose resolved destination path falls outside the specified target directory should be silently skipped. This includes entries using single or nested relative traversal sequences, as well as entries with Unix-style or Windows-style absolute paths.
- Safe entries (those that remain within the target directory) should still be extracted normally.
- When the top-level directory stripping mode is active, unsafe entries should also be excluded without affecting external files.
- A utility function that strips a leading directory prefix from archive entry paths should validate its directory argument and reject values that are empty, self-referential, parent-directory references, or absolute paths (either Unix or Windows style), raising an error for those cases.

## Why This Matters

Without this protection, a runtime environment packaged as a specially crafted zip file could silently overwrite sensitive files during extraction, posing a serious security risk in multi-tenant or automated deployment scenarios where archive contents cannot be fully trusted.
