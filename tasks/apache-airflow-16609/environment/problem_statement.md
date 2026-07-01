## Description

The REST API for user management is incomplete — it only supports reading users (listing and retrieving individual user records), but provides no way to create, update, or delete users. This means that any automated workflow that needs to manage the full lifecycle of users (provisioning, role updates, deprovisioning) cannot use the API and must rely on other mechanisms.

## Expected Behavior

- Administrators should be able to create new users via the API by providing a username, password, email, first name, last name, and optionally a list of roles. If no roles are specified, a sensible default role should be assigned automatically.
- Administrators should be able to update an existing user's profile or role assignments via the API, with support for selectively updating only a subset of fields using an update mask.
- Administrators should be able to delete a user via the API.
- When a write request is made by an unauthenticated caller, the API should return a 401 response. When the caller lacks the appropriate permission (create, edit, or delete), a 403 response should be returned.
- Attempting to create a user with an already-taken username should return a 409 conflict response.
- Attempting to update or delete a user that does not exist should return a 404 response.
- Submitting a request with missing required fields, unknown fields, or role names that don't exist in the system should return a 400 response with a descriptive error message identifying exactly what was wrong.

## Why This Matters

Without write support in the API, user management cannot be automated through the standard interface. Adding create, update, and delete operations makes it possible to fully manage users programmatically, bringing the user management API to parity with other resource types that already support the full set of operations.
