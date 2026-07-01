## Description

When a directory has had all its permissions removed (completely locked down), the application's directory accessibility check incorrectly reports it as accessible, returning no error. This causes the application to silently proceed with a directory that cannot be read or written, rather than reporting a clear access problem.

## Expected Behavior

- When a directory exists but its permissions are fully restricted, the accessibility check should return an error indicating the directory is inaccessible.
- The error message should clearly state that the directory is inaccessible and advise the user to check access rights.

## Why This Matters

The current behavior causes silent failures: the application proceeds as if a locked-down directory is usable, which leads to confusing errors later on. Users should receive an immediate, clear error indicating an access rights problem rather than encountering mysterious failures downstream. This fix ensures the accessibility check is reliable even for directories with extreme permission restrictions.
