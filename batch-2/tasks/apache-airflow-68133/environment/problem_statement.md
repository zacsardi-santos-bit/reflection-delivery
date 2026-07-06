## Description

The state store does not enforce a configurable size limit on stored values. Currently, if a task or asset stores a very large value, the API accepts it without any size-based validation. There is also no client-side feedback when task code attempts to store an oversized value.

## Expected Behavior

- The state store API endpoints (both for tasks and assets) should enforce a maximum byte size for stored values. Requests that exceed this limit should be rejected with a validation error.
- The size limit should be configurable. When the limit is set to zero, it should be treated as disabled, allowing values of any size to be accepted.
- Client-side accessor code (running inside a task) should emit a warning when a value being stored exceeds the configured maximum size. The warning should not block the operation — the store call should still proceed — but the developer should be alerted.

## Why This Matters

Without a size limit, large values can be inadvertently stored in the state store, leading to excessive database storage usage and potential performance degradation. Providing both a server-side enforcement mechanism and a client-side early warning gives operators control over storage growth while giving developers immediate feedback during development.
