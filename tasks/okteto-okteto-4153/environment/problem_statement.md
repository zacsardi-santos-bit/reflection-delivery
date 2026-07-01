## Description

The codebase currently depends on an outdated, unmaintained library for parsing Git URLs. This library needs to be replaced with a modern, actively maintained alternative that provides the same functionality.

The old library is declared as a direct dependency in the module configuration and is imported in multiple places throughout the codebase. The replacement library offers equivalent Git URL parsing capabilities and should be used as a drop-in replacement.

## Expected Behavior

- The module dependency configuration should reference the new Git URL parsing library instead of the old one
- All source files that import the old library should be updated to import the new library
- The module checksum file should be updated accordingly
- All existing functionality that relied on the old library should continue to work correctly, including:
  - Parsing HTTPS Git URLs (with and without trailing slashes, with and without `.git` extensions)
  - Parsing SSH Git URLs (with and without `.git` extensions)
  - Handling invalid, empty, or incomplete Git URLs gracefully

## Why This Matters

Using an unmaintained dependency introduces security and compatibility risks. Updating to a maintained alternative ensures the project stays current and can receive future bug fixes and security patches. The change should be transparent to users — all existing Git URL parsing behavior must be preserved.
