## Description

The object storage test suite in JuiceFS currently has several problems that make it difficult to maintain and run in CI/CD environments:

1. **Hardcoded endpoints**: Test code contains hardcoded endpoint URLs for each cloud storage provider. To run tests against a different region or account, developers must modify source code directly.

2. **SQLite driver misplacement**: The database driver for SQLite-backed object storage is registered only in the test file, not in the implementation. This makes the implementation non-self-contained — anything importing the SQL store package outside of tests won't have the SQLite driver available unless the test file's import is duplicated.

3. **Missing providers**: Several widely-used cloud storage backends (EOS, Wasabi, SCS, IBM Cloud Object Storage) are not yet supported.

4. **Obsolete providers**: Two storage provider implementations are stale and should be removed.

## Expected Behavior

- All cloud provider tests should read their endpoint URL and credentials from environment variables, and skip the test immediately when those variables are not set.
- The SQL store implementation file should own the database driver registrations for all supported SQL backends (SQLite, MySQL, PostgreSQL) so it works correctly regardless of how the package is used.
- New provider support (EOS, Wasabi, SCS, IBM Cloud Object Storage) should be added so those providers can be tested.
- Obsolete providers (MSS, Yovole) should be removed from both the test suite and the implementation.

## Why This Matters

Hardcoding endpoints into tests means CI pipelines can't be reconfigured without touching source code. Having the SQLite driver registered only in the test file is fragile and violates the principle that an implementation package should be self-sufficient. Adding missing providers expands the range of storage backends users can choose from.
