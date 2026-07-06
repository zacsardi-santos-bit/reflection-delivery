## Description

The commitlint CLI package currently uses a different testing framework than the rest of the monorepo. This means the CLI tests cannot be run with the same single command used for the rest of the project, creating inconsistency in the developer workflow and CI pipeline.

The goal is to migrate the CLI package's tests to the same testing framework used across the rest of the monorepo, and update the project-wide test runner configuration to include the CLI package's test files. Once this is done, the old testing framework and its configuration should be completely removed from the CLI package.

## Related Changes

As part of this migration, the shared test utility package (used to set up fixture directories for tests) needs to be updated so its bootstrap functions accept an optional directory hint. This allows callers to specify where fixture lookup should begin, rather than always relying on the current working directory. This makes test setup more reliable when tests are run from different locations in the repository.

## Expected Behavior

- Running the project-wide test command should now also execute the CLI package's tests.
- The CLI package should no longer have any configuration or dependency on the old testing framework.
- The shared test utilities should support an optional directory parameter in their bootstrap functions, enabling reliable fixture resolution relative to any given path.
- The shared test utility package should properly declare its TypeScript types.

## Why This Matters

Consolidating all tests under a single framework and runner simplifies maintenance, reduces configuration surface area, and ensures the CLI is always tested as part of the standard CI flow without needing special handling.
