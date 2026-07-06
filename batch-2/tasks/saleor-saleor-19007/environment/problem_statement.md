## Description

When querying attributes filtered by a product category or collection, the API can return the same attribute multiple times in the result list. This happens when a single attribute is assigned to more than one product type, and products from those different product types all exist in the same category or collection.

## Expected Behavior

- Filtering attributes by a category should return a deduplicated list of attributes — each attribute must appear at most once, even if multiple product types in that category share the same attribute.
- Filtering attributes by a collection should return a deduplicated list of attributes — each attribute must appear at most once, even if multiple product types in that collection share the same attribute.
- A shared attribute that qualifies for the filter must still appear in the results — it should not be omitted, just not repeated.
- This deduplication must work regardless of whether the query uses the legacy filter input or the newer where input style.

## Why This Matters

Any client consuming the attributes list for a category or collection facet (e.g., storefront filters) will encounter duplicate entries, breaking UI components or downstream logic that assumes each attribute appears once. This is a data correctness bug that makes the attribute filtering API unreliable whenever attributes are shared between product types.
