## Description

When generating authentication tokens for users, the system crashes if the user's stored document is missing data for field groups or tabs that were later added to the collection schema. This causes a property access error, making it impossible to authenticate those "legacy" users.

## Expected Behavior

- Building an authentication token for a user document that is missing group or tab field data should succeed without throwing an error.
- The resulting token payload should still include the user's core identity information (their ID, email address, and collection name) even when some group/tab field data is absent.
- Missing group or tab data should be treated as empty rather than causing a crash.

## Steps to Reproduce

1. Add group or tab fields to an existing auth collection's schema.
2. Attempt to log in (or refresh a token) as a user whose stored document predates those schema changes — meaning the group/tab fields are not present in the stored data.
3. Observe the crash / error response.

## Why This Matters

Schemas evolve over time. Users who registered before a group or tab field was introduced will have documents that lack that data. These users should still be able to log in successfully — the token-building process should be resilient to missing optional field data.
