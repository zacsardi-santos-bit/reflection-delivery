## Description

Home Assistant currently allows users to stop or restart the system even when a database migration is actively in progress. This is unsafe — interrupting a migration mid-way can cause database corruption or data loss. There's also no way for other parts of the system to easily check whether a migration is currently running.

Additionally, when a restart is triggered remotely (e.g., via the WebSocket API), the call is made in a non-blocking way, which can lead to race conditions or unexpected behavior.

## Expected Behavior

- A new helper should be available that checks whether the database recorder is currently performing a schema migration. This helper should safely return false when the recorder isn't even loaded, and correctly report the migration state when it is.
- The stop and restart services should refuse to execute if a database migration is in progress, raising an appropriate error with a clear message explaining why.
- The restart service should also continue to validate configuration before restarting, raising a clear error if the configuration is invalid.
- The stop service should NOT require a valid configuration before stopping — the configuration check is only relevant to restart.
- The actual stop/restart action should happen asynchronously with a short delay rather than immediately inline.
- Remote restart calls via the WebSocket interface should be blocking.

## Why This Matters

Stopping or restarting during a database migration is a dangerous operation that can leave the database in a corrupt or inconsistent state. Users need the system to protect them from this scenario automatically, and other system components need a reliable way to query migration status.
