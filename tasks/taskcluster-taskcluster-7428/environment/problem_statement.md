## Description

When a generic worker runs multiple tasks sequentially and those tasks share a writable directory cache, the cache directory is passed from one task user account to the next. The current approach grants the new task user access to the directory when mounting it and then revokes that access when unmounting. This causes problems when tasks use containerized environments that rely on specific user ID mappings — particularly when containers manage sub-user ID ranges — because revoking access at unmount time can leave the cache in a state that is inaccessible to future tasks.

## Expected Behavior

- When a writable directory cache is mounted, the worker should transfer file ownership of the cached directory contents to the new task user, rather than simply granting read/write permissions.
- The worker should log an informational message indicating that ownership is being updated from the previous task user to the new task user, including both the source and destination usernames.
- When a writable directory cache is unmounted, the worker should NOT revoke or deny access to the directory. The ownership transfer on mount makes revocation unnecessary.
- In non-multiuser (insecure) builds, no ownership transfer is performed or logged.

## Why This Matters

The current grant/revoke model is fragile when tasks run inside containers with user namespace mapping. Transferring ownership at mount time instead produces a more robust handoff between task users, eliminates unnecessary permission churn at unmount time, and makes writable directory caches work reliably across containerized task environments.
