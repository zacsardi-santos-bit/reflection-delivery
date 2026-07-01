## Description

The SQL logic test runner currently requires being launched from a specific nested subdirectory, so every test script must navigate two directory levels upward to reach shared test data. This makes the runner awkward to invoke and the path references in test scripts hard to read.

We should change the test runner so that it operates from the project root directory. Once it does, all relative data file references in SQL test scripts can simply start from the root rather than traversing up two levels. For example, a path that currently navigates up two directory levels before reaching the testdata folder should instead be a root-relative path starting directly with the testdata folder.

## Expected Behavior

- The SQL logic test runner binary can be invoked from the project root directory.
- All SQL test scripts that reference local data files use paths relative to the project root, starting directly from the testdata folder.
- Queries that scan parquet, CSV, JSON, delta, and lance files using relative paths continue to return the correct results.
- Glob patterns with root-relative paths still work correctly.
- Direct file-path queries, file type inference, CREATE TABLE AS SELECT, CREATE VIEW, and CREATE EXTERNAL TABLE operations all work when given the new relative paths.
- Tests for unknown file extensions and missing extensions continue to produce the appropriate errors.

## Why This Matters

Having the test runner operate from the project root makes the test suite much simpler to invoke in CI and local development. Path references in the test scripts become self-evident, and contributors no longer need to know which subdirectory to cd into before running tests.
