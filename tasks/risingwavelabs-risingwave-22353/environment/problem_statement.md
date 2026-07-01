## Description

Our MySQL async connection pool integration test currently lives inside the connector package, which is a large, unrelated package with many other responsibilities. This makes it hard to run MySQL-specific connectivity tests in isolation, and it unnecessarily couples the test to a heavy package build.

We should extract this test into its own dedicated test crate within the workspace, so that:
- MySQL integration tests have a proper, focused home
- CI can invoke MySQL-specific tests without depending on the entire connector package
- The test infrastructure is clearly organized by concern

## Expected Behavior

- A new, self-contained test package for MySQL async integration tests exists in the workspace
- The workspace build system recognizes the new package and can compile it independently
- Running tests for both the new MySQL test package and the SQL parser package together succeeds without errors
- The CI pipeline is updated to run the MySQL integration tests from the new dedicated package instead of the old location

## Why This Matters

Keeping integration tests co-located with unrelated production code makes the project harder to navigate and slows down targeted test runs. A dedicated test crate is easier to maintain, easier to run selectively, and signals clearly what it tests.
