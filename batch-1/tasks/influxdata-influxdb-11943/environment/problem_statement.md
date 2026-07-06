## Description

Users who authenticate via a browser session (rather than a direct API token) are unable to create scheduled tasks through the API. The task engine requires an authorization token to run tasks unattended, but when a creation request comes from a session-authenticated user, no such token is generated or stored — causing the task creation to fail or produce a task that can never execute.

Additionally, there is currently no mechanism for the system to determine ahead of time what permissions a particular query will require at runtime. Without this, it is impossible to automatically provision the right scoped authorization token at task creation time.

## Expected Behavior

- When a session-authenticated user creates a task, the system should automatically compute the permissions required by the task's query script, create a new authorization token with those permissions, and associate that token with the task — all transparently during the task creation request.
- The ability to inspect a compiled query specification and enumerate its required permissions must be available. For queries that write to a bucket, this should produce a write permission scoped to that specific bucket (identified by its ID and organization ID, not just its name).
- The automatically created token must be persisted so it can be retrieved later by the task engine.
- Task creation from a session should return a successful response (HTTP 201) with a valid task record.

## Why This Matters

Without this capability, browser-based or session-authenticated users are effectively blocked from creating tasks via the API. There is also no automated way to provision task credentials scoped to exactly what the task needs, making it difficult to securely support background task execution.
