## Description

The SQL datastore plugin's configuration method does not accept a context parameter, making it inconsistent with Go's standard API conventions for I/O-bound operations and preventing proper cancellation or timeout propagation during database initialization.

All other database operations in the plugin already accept a context, but the configuration step — which opens database connections and may perform other setup work — is missing this parameter. This is a gap that needs to be closed.

## Expected Behavior

- The method that configures the SQL datastore should accept a context as its first parameter
- All call sites that invoke this configuration method (including production startup paths, test helpers, and fake implementations used in tests) should be updated to pass a context
- Existing error handling behavior (invalid database types, malformed connection strings, missing required parameters) must continue to work as before

## Why This Matters

Without a context parameter, there is no way to propagate deadlines or cancellation signals into the database initialization phase. This makes it impossible to perform context-aware work (such as cleanup of stale data) as part of startup, and it breaks consistency with the rest of the codebase's API conventions. Adopting this pattern also enables the initialization routine to safely perform additional database operations that require a context.
