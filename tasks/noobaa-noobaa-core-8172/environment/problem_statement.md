## Description

The IAM operations (user management and access key management) do not currently validate their input parameters before processing begins. When a required field like a username or access key ID is missing entirely, or when a value violates a constraint — such as a username being empty, a path not starting and ending with the correct delimiter, or an access key ID being shorter than the allowed minimum — the operation proceeds without raising an informative error at the boundary. Errors only appear later in processing, making it hard to diagnose what went wrong.

## Expected Behavior

- Each IAM operation handler should check its input parameters as early as possible and reject malformed requests with a clear validation error.
- A missing required parameter should result in an error whose message communicates that the field is required.
- A value that violates a length constraint should produce an error whose message references the length issue.
- A path value that lacks the required leading or trailing delimiter should produce an error referencing the path problem.
- A status value that is not one of the two accepted values should produce an error indicating the constraint was not satisfied.
- Reusable validation utility functions should be provided (for paths, usernames, markers, access key IDs, and status values) so that each operation handler can apply consistent validation logic.
- A new constants module should be introduced so that field names used in validation messages are centralized rather than duplicated.

## Why This Matters

Without input validation at the operation boundary, invalid requests pass silently into the system and produce confusing failures. Adding early, consistent validation makes the API easier to use and debug, and helps developers understand exactly what constraints apply to each field.
