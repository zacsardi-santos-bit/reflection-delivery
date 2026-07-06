## Description

The NooBaa NC NSFS health check cannot properly validate accounts that are identified by an OS username rather than by explicit numeric user and group IDs. When an account's storage configuration references an OS username, the system currently has no way to verify whether that user actually exists or whether they have read-write access to the configured storage path. This means the health check silently skips or incorrectly handles these accounts, allowing misconfigured or invalid setups to go unreported.

## Expected Behavior

- When an account is configured with an OS username identity and that user exists but does not have read-write access to the configured storage path, the health check should flag the account as invalid with an access-denied error.
- When an account is configured with an OS username that does not exist on the system, the health check should flag the account as invalid with a distinct "invalid distinguished name" error code.
- Filesystem path accessibility checks must work correctly for both username-based identities and numeric ID-based identities, properly evaluating standard unix permission bits (owner, group, other) to determine whether a directory is both readable and writable.

## Why This Matters

Operators rely on the health check to detect misconfigured accounts before they cause runtime failures. Without this fix, accounts with username-based identities could silently pass health validation even when the OS user doesn't exist or cannot access the required storage, leading to unexpected errors during normal operation.
