## Description

When querying attributes filtered by a specific category or collection, the API returns duplicate attributes if those attributes are shared across multiple product types. For example, if two different product types both include the same attribute, and both product types have products in the same category or collection, querying for attributes in that category/collection returns the shared attribute twice — once per product type that references it.

## Expected Behavior

- Attributes filtered by category should appear exactly once in the results, regardless of how many product types in that category use the attribute.
- Attributes filtered by collection should appear exactly once in the results, regardless of how many product types in that collection use the attribute.
- A shared attribute should still be included in results — it must not be excluded, just deduplicated.

## Steps to Reproduce

1. Create two product types that share at least one attribute.
2. Create one product of each type, placing both in the same category (or collection).
3. Query the attributes API filtered by that category (or collection).
4. Observe that the shared attribute appears multiple times in the response.

## Why This Matters

Duplicate attributes in the response can cause incorrect behavior in any UI or downstream system that relies on this API — for example, showing the same filter option multiple times in a storefront faceted search. The fix should ensure uniqueness of returned attributes for both category-based and collection-based attribute lookups.
