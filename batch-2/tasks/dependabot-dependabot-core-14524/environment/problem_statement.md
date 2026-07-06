## Description

When a Python project uses Poetry as its package manager but does not have a lockfile committed to the repository, the dependency graph scanner returns only partial information. Package names are listed without resolved version numbers, and no transitive dependency relationships are available. This makes it impossible to build a complete and accurate dependency graph for such projects.

## Expected Behavior

- When no lockfile is present for a Poetry-based Python project, the scanner should attempt to generate a temporary lockfile on the fly and use it for dependency resolution.
- If temporary lockfile generation succeeds, the scanner should return exact resolved versions and full transitive dependency relationships.
- If temporary lockfile generation fails, the scanner should fall back gracefully and return the available data without relationship information (rather than failing entirely).
- The reported "relevant dependency file" for the project should always be the project manifest file, not any temporarily generated lockfile.

## Related Fix

Additionally, the npm/yarn dependency scanner emits a warning message informing users that a temporary lockfile was generated. Currently this warning is emitted even when lockfile generation fails, which is misleading. The warning should only be shown when generation actually succeeded.

## Why This Matters

Without this change, Python Poetry projects that don't commit their lockfile receive incomplete dependency graph data. Enabling on-the-fly lockfile generation allows Dependabot to provide accurate version resolution and relationship data for these projects, improving security scanning quality.
