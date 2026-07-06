# SCIM Server: Validation, Error Handling, and External Identifier Persistence Gaps

## Description

The SCIM server implementation has several related issues that need to be addressed together:

1. **Error responses can't be parsed back by clients.** The SCIM specification requires the HTTP status code to be written as a quoted string inside the error JSON body. Our error type already serializes correctly, but there is no way to deserialize an error response back into a typed struct — the status-as-string format is not handled during parsing. Any code that reads SCIM error responses back into structured objects fails.

2. **Group member additions have no pre-flight validation.** When adding members to a group (during create, update, or patch operations), the server processes each addition one at a time without first verifying that all members are valid. If a problem is discovered partway through, some members have already been added, leaving the group in a partially-updated state. Nested groups (a group being referenced as a member of another group) are also not being rejected at the right point in the request lifecycle.

3. **External identifier changes are silently dropped when a group is looked up by ID.** When a create or update request provides an external identifier that differs from what is stored, and the group is found by its internal identifier rather than by display name, the new external identifier is not persisted. This causes the group's external identifier to become stale.

4. **Cache failures during user operations are swallowed.** When the user attribute cache returns an unexpected error during user listing or user retrieval, the server silently skips the affected user rather than reporting an error. This masks real infrastructure problems.

## Expected Behavior

- All SCIM error responses should carry a structured error body parseable as a typed object
- Group member operations should validate all members before making any changes
- Members that are groups (nested groups) should be rejected with an appropriate 400 error
- Members whose user ID does not exist should be rejected with a 404 error
- External identifier updates should be persisted even when the group is found by internal ID
- Non-transient cache failures during user operations should return a 500 error to the client

## Why This Matters

These gaps create operational problems: partial group updates are hard to debug, clients cannot reliably distinguish error types, stale external identifiers break federation scenarios, and real infrastructure errors are invisible.
