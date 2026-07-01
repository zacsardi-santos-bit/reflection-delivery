## Description

The Gravitino metadata caching layer needs a dedicated, strongly-typed key class to represent entries in an entity cache. Currently, there is no standard way to identify cache entries that must distinguish between a directly stored entity and an entity that is cached as part of a relationship (e.g., "which roles does this user belong to?"). Both kinds of entries share the same entity identity, so a key that only holds a name identifier and entity type is not sufficient.

## Expected Behavior

- A new cache key type should bundle an entity's name identifier, entity type, and an optional relationship category into a single immutable object.
- The key should produce a compact, human-readable string that encodes all its components, using the entity type's short name and the relationship category's identifier when present.
- When no relationship type is supplied, the string representation should omit that component.
- The key should validate that the name identifier and entity type are provided (non-null), while allowing the relationship type to be absent.
- The key should correctly implement equality and hashing based on all three components, so it can be used as a map key with predictable lookup behavior.

## Why This Matters

Without a dedicated key type, the entity cache cannot reliably differentiate between a cached entity and a cached relation for that same entity. Having a well-defined key class with a clear string format makes cache debugging easier and ensures correct cache behavior when both direct and relation-based entries coexist for the same entity.
