## Description

Currently, API keys used for runtime authentication are flat strings with no concept of access level. Every valid key grants the same permissions — including the ability to push data into the system via the flight protocol. This means there is no way to give some clients read-only access while giving others write access.

Additionally, the flight data ingestion endpoint currently performs no meaningful authorization check. Unauthenticated requests are not rejected, and there is no mechanism to distinguish between clients that should be allowed to write data and those that should not.

## Expected Behavior

- API keys should support two access levels: read-only and read-write.
- Keys should be expressed as strings with an optional suffix that designates the access level. Keys without a recognized suffix default to read-only.
- When comparing an incoming bearer token against a configured key, only the key portion (before any access-level suffix) should be used for matching — so a bearer token authenticates correctly against a key that carries an access-level suffix.
- The flight data ingestion endpoint must enforce authorization:
  - Unauthenticated requests must be rejected with an error.
  - Requests authenticated with a read-only key must be rejected with an error.
  - Requests authenticated with a read-write key must be permitted to proceed.

## Why This Matters

Administrators need to distribute API keys with different permission levels to different clients. Without access levels, any client with any valid key — or even no key at all — can push data into the system. Enforcing write-access control on the data ingestion path is necessary for secure multi-client deployments.
