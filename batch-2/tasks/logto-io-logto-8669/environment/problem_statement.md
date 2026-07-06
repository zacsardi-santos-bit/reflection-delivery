## Description

We need to add support for scheduled signing key rotation in our multi-tenant OIDC system. Currently, when an administrator stages a new signing key (marking it as "next"), there is no mechanism to automatically promote it to active status at a specific future time. The system also has a gap: cache invalidation only writes to the distributed cache (Redis), with no corresponding persistence to the database.

## Expected Behavior

- Administrators should be able to record a future timestamp specifying when a staged signing key should become the active signing key.
- The scheduled rotation timestamp, along with the cache invalidation timestamp, should be persisted to the database — not just held in Redis.
- When a tenant checks whether it is still healthy, it should consult the database-backed signing key rotation state (using the distributed cache as a read-through layer to avoid redundant database queries).
- When the scheduled time for a staged key arrives, the tenant should report itself as unhealthy so it gets recreated.
- During tenant recreation (specifically during environment setup), the system should automatically promote any staged signing key whose scheduled activation time has passed, before loading OIDC configuration.
- A helper function should exist to transition key statuses: promoting the staged key to active and demoting the previously active key to the retired state, and returning the original key set unchanged when no staged key is present.

## Why This Matters

Without this, staged signing keys can only be activated immediately (by forcing full cache invalidation). There is no way to pre-stage a key and have it go live at a predictable future time. This is important for zero-downtime key rotation workflows where operators want to publish the new key to the JWKS endpoint ahead of time, then switch to signing with it only after a grace period.
