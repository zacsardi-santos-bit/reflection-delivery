## Description

There is currently no structured tooling for contributors to document new features as they are developed, or for maintainers to compile those feature descriptions into formatted release notes. Without such a tool, tracking what's new in a given release requires manually reviewing commits and pull requests, which is error-prone and tedious.

We need a feature generation tool that:

- Allows contributors to create a new feature announcement file from a standard template, giving it a name or using a default.
- Validates pending feature files to ensure they include all required fields: a recognized component category, at least one linked issue, a one-line description, and an author.
- Compiles all valid pending feature files into a single formatted release notes document, grouping entries by component and linking issues to the project's issue tracker.
- Supports a "dry run" mode that previews the output without writing files.
- On final release, archives the pending feature files into a versioned folder so the history of which features landed in which release is preserved.

## Expected Behavior

- A new feature stub file can be created with a given name; invalid names (containing path separators or special characters) are rejected.
- Feature files with a missing or empty issues list, or an unrecognized component name, are reported as invalid.
- The formatted output document includes a header with the version and today's date, groups features by component, and formats each entry with description, author, and linked issue numbers.
- A "final release" run moves the pending files into a versioned archive directory.

## Why This Matters

Automating feature documentation reduces manual effort at release time and ensures that every merged feature has a properly attributed, consistently formatted entry in the release notes.
