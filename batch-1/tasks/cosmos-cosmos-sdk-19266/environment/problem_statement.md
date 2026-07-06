## Description

On the Cosmos Hub LSM (Liquid Staking Module) deployment of the SDK, the query for looking up all accounts that hold a given token denomination should be disabled, because the underlying reverse index that this query depends on was never built during this chain's state migration.

Currently, the tests expect this query to succeed for valid denom inputs — returning a list of token holders with pagination support. However, the underlying index is not available on this chain, so these requests always fail in practice. The tests are inconsistent with the actual runtime behavior.

## Expected Behavior

- Any request to look up token holders for a given denomination (with any valid denom and any pagination parameters) must result in an error response.
- The response object must be nil when the query fails.
- This behavior should apply uniformly to all non-nil requests — regardless of whether the denomination exists, has associated balances, or how many accounts hold it.

## Why This Matters

Without this fix, clients and tests believe this query should work and return account data, but it actually fails at runtime because the required store index is absent. This causes test failures and misleads integrators about what queries are available. Returning an explicit error for all requests makes the limitation clear and consistent, allowing users to know they should rely on an external indexer for this information instead.
