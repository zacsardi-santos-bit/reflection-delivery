## Description

The database migration commands cannot be used in automated integration tests because certain operations (like resetting the database with a fresh migration) always pause and wait for interactive user confirmation before proceeding. This blocking behavior makes it impossible to run migration tests in CI or any non-interactive environment.

Additionally, migration files for all test suites currently get stored in a single shared directory, which means test suites can interfere with each other's migration state.

## Expected Behavior

- Migration commands should support a "force accept" mode that bypasses interactive prompts and proceeds automatically — useful for scripted and test environments.
- When a fresh migration is run in this mode, it should drop existing migrations, re-apply them, and record the results correctly in the migrations tracking collection (with the right name and batch number).
- Each test suite should use its own isolated migration directory rather than sharing a global one, so migration state doesn't leak between test runs.
- A shared utility should exist to configure the environment paths (config path and type output path) for a given test suite directory, and this utility should be reusable across different test tooling scripts.

## Why This Matters

Without the ability to bypass interactive prompts, it's impossible to write automated tests that verify the full migration lifecycle — create, apply, check status, and reset. This blocks development of reliable integration tests for the database layer.
