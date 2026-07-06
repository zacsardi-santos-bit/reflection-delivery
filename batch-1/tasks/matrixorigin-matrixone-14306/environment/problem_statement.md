## Description

When creating or dropping a user-defined function using a database-qualified name (e.g., specifying the target database as part of the function name), the system does not currently verify that the specified database actually exists. This allows operations to proceed against non-existent databases, leading to confusing downstream errors or unexpected behavior rather than an immediate and clear rejection.

## Expected Behavior

- Attempting to create a user-defined function with a database-qualified name, where that database does not exist, should immediately fail with a clear error indicating that the specified database does not exist.
- Attempting to drop a user-defined function with a database-qualified name, where that database does not exist, should immediately fail with a clear error indicating that the specified database does not exist.
- When the target database does exist, both operations should proceed normally.

## Why This Matters

Without this validation, users receive confusing error messages that do not explain the root cause of the failure — the database they referenced simply doesn't exist. Adding an upfront check makes it immediately obvious what went wrong, reducing debugging time and preventing operations from running against an invalid state. The fix should also include a shared helper that checks whether a given database exists, following the same pattern used by existing tenant and system existence checks.
