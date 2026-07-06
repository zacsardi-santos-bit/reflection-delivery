## Description

Qdrant's recommendation feature supports looking up reference vectors from a separate collection — this is useful when the "example" points live in a different collection than the one being searched. This cross-collection lookup capability already works in the dedicated recommendation endpoint, but the general-purpose query endpoint does not support it. As a result, users who try to run recommendation queries with cross-collection lookups via the query endpoint get no results or incorrect behavior.

Beyond the missing feature, there are also two related problems:

1. **Access control gap**: The authorization system does not check whether the requesting user has permission to access the collection referenced for vector lookup. A user with restricted access to only specific collections can currently bypass those restrictions by referencing other collections through the lookup mechanism.

2. **Missing validation**: When a fusion-based query (like reciprocal rank fusion) is combined with a vector name selector in the same query request, no error is returned even though this combination is meaningless — the system silently ignores one of the inputs.

## Expected Behavior

- The query endpoint should support cross-collection vector lookup at both the top level and within nested prefetch queries, with results identical to what the dedicated recommendation endpoint returns.
- When a referenced lookup collection, point ID, or vector name does not exist, a clear descriptive error message should be returned.
- Access to the lookup collection should be enforced under JWT-based authorization — attempting to look up from an unauthorized collection should be rejected with a permission error.
- Combining a fusion query with an explicit vector name selector should be rejected with a validation error.

## Why This Matters

Users rely on the general-purpose query endpoint for composable, complex queries. Without cross-collection lookup support in this endpoint, they must fall back to the more limited dedicated recommendation endpoint, losing the ability to combine recommendation with prefetch, fusion, or other advanced query features.
