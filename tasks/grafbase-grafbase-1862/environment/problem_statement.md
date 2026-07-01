## Description

The partial caching system doesn't work correctly when a GraphQL query uses type conditions — specifically when a query field returns a union of types or an interface and the client requests different fields for each concrete type using type-specific fragments.

When partial caching is enabled, the system needs to know the concrete type of each returned object in order to decide which cached fields apply to it. Without this information, the system cannot correctly merge cached data for queries that include inline or named fragments targeting specific concrete types.

## Expected Behavior

- Queries that use inline fragments to request different fields from different members of a union should produce correct, complete responses when partial caching is active.
- Queries that use named fragments on union or interface types should behave the same way.
- The system should automatically include type metadata in any sub-queries it generates, so that cache entries can later be matched to the correct concrete type.
- All of the above should work correctly when combined with deferred loading.

## What Was Broken

If a schema defines a union of types and a query requests type-specific fields (e.g., one set of fields for type A and another for type B in the same list), the partial caching layer would not be able to determine which cached entry belonged to which concrete type. As a result, the cache merge step could produce incorrect or incomplete responses.

## Why This Matters

Schemas that use unions or interfaces — a very common pattern — should be fully compatible with partial field-level caching. Without this fix, developers must disable partial caching any time their schema or queries involve type conditions, significantly reducing the feature's usefulness.
