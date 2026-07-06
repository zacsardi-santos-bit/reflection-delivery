## Description

The project currently uses a mainstream JWT library for all authentication operations, but we need to switch to a SurrealDB-compatible fork of that same library. This is needed for better alignment with the SurrealDB ecosystem and to avoid compatibility issues when SurrealDB-specific features are used alongside JWT authentication.

## Expected Behavior

- The project dependency should use the SurrealDB-maintained JWT fork instead of the original library
- All JWT authentication operations must continue to work as before: token verification, audience and issuer claim validation, JWKS key set handling (both local and remote), and key ID matching
- The web key set types from the new library must be properly serializable and deserializable from JSON, including the ability to represent an empty set of keys

## Why This Matters

The SurrealDB ecosystem provides its own JWT library fork that may have subtle API differences. Mixing the original library with SurrealDB-specific code can lead to incompatibilities. Switching entirely to the SurrealDB fork ensures consistent behavior and simplifies the dependency tree for projects that already rely on SurrealDB. Any code that currently works for JWT authentication — including token validation, audience checks, issuer checks, and remote JWKS fetching — should continue to function correctly after the switch.
