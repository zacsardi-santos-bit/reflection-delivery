I'm seeing a bug in our attribute filtering: when I query attributes scoped to a specific product category or collection, the same attribute shows up multiple times in the results.

*   When filtering attributes by a product category (via the inCategory filter), the response must contain no duplicate attributes — each attribute must appear at most once, even if the same attribute is assigned to multiple product types that have products in that category.

*   When filtering attributes by a collection (via the inCollection filter), the response must contain no duplicate attributes — each attribute must appear at most once, even if the same attribute is assigned to multiple product types whose products belong to that collection.

*   Deduplication must apply to both the legacy 'filters' input variant and the newer 'where' input variant of the attributes GraphQL query.

*   An attribute that is shared across multiple product types must still appear in the filtered results — it must be present exactly once, not omitted entirely.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.