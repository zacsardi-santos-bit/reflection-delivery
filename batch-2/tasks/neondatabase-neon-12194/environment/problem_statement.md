## Description

The layer manager's lock acquisition mechanism needs to be updated so that every caller must declare who is acquiring the lock. Currently, the read and write lock methods on the layer manager accept no arguments, meaning any component can acquire a lock without leaving any record of which part of the system is holding it. This makes it difficult to diagnose lock contention, track ownership, and reason about which code paths hold locks at any given time.

## Expected Behavior

- The layer manager's read and write lock methods should require callers to provide an identifier that names the component or context acquiring the lock.
- A dedicated identifier variant should be provided for use in test code, so tests can acquire locks without impersonating production components.
- All existing callers throughout the pageserver must be updated to pass an appropriate identifier.

## Why This Matters

Without a holder identifier, lock acquisition is anonymous, which complicates debugging and observability. By requiring each lock acquisition to declare itself, the codebase gains a clear record of which subsystems hold locks, making future debugging and deadlock analysis significantly easier. This is a breaking API change that must be applied consistently across all callers to restore compilation.
