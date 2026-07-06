## Description

The migration system does not validate the metadata it fetches when calculating which packages to update. When a fetched migration configuration is missing required version information, the tool fails silently or crashes later in an uninformative way rather than surfacing a clear error at the point of the problem.

Additionally, when the migration tool runs package manager commands and those commands fail, the useful diagnostic output produced by the package manager (such as registry errors or fetch failures) is not included in the error shown to the user — making it hard to understand what actually went wrong.

## Expected Behavior

- If fetched migration metadata for a package is missing its version information, the migration should fail immediately with a clear message identifying the affected package.
- If a package update referenced within a parent package's migration metadata is missing version information and that update would be applied, the migration should fail immediately with a message identifying both the parent package and the affected child package.
- If a package update with missing version information belongs to an update group that is skipped (because its conditions are not met), the migration should proceed normally without error.
- When a package manager command fails during migration, the error output from the package manager should be included in the reported failure alongside the failure message.

## Why This Matters

These issues mean that corrupted or incomplete migration metadata causes confusing downstream failures rather than actionable errors, and that package manager failures during migration are hard to diagnose because their detailed error output is not surfaced. Better upfront validation and error formatting helps developers quickly identify and fix the root cause of migration failures.
