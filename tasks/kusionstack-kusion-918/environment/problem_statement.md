## Description

The OCI client package currently has no way to package local files or directories into compressed archives for use as OCI artifacts. We need a function that takes a local path (either a file or a directory) and produces a gzip-compressed tar archive suitable for pushing to an OCI registry.

## Expected Behavior

- When given a directory path, the function packages all contents into a tar.gz archive at the specified output path.
- When given a single file path, the function packages just that file into a tar.gz archive.
- Both relative and absolute paths should be supported, including paths with a leading dot-slash prefix.
- The function should accept a list of ignore patterns (similar to gitignore rules) that exclude matching files and directories from the archive. Negation patterns should work correctly to override exclusions.
- If the source path does not exist, the function should return an error.
- Supporting test data (sample YAML files, ignore-test fixtures) must be included under the package's testdata directory.

## Why This Matters

Without this capability, there is no way to create OCI artifact archives from local project files. This is a foundational step for implementing OCI push/pull workflows in the Kusion toolchain.
