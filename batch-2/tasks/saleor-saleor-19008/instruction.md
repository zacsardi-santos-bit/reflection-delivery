I'm seeing duplicate attributes returned from our product attribute API when filtering by category or collection.

*   When the attributes GraphQL query is called with an 'inCategory' filter and a channel slug, and multiple product types in that category share the same attribute, the response must contain each attribute exactly once — no duplicate attributes should appear in the result set.

*   When the attributes GraphQL query is called with an 'inCollection' filter and a channel slug, and multiple product types in that collection share the same attribute, the response must contain each attribute exactly once — no duplicate attributes should appear in the result set.

*   The deduplication requirement applies to both the legacy 'filter' parameter (filters: {inCategory: ...} / filters: {inCollection: ...}) and the newer 'where' parameter (where: {inCategory: ...} / where: {inCollection: ...}) variants of the attributes query.

*   A shared attribute that is referenced by multiple product types must still be present in the query results — it should appear exactly once, not be excluded entirely.

*   The fix must be applied in the function filter_attributes_by_product_types located in saleor/graphql/attribute/filters.py, which is the shared implementation used by both filter and where attribute query paths.


*   Interface details: Type: Function
Name: filter_attributes_by_product_types
Location: saleor/graphql/attribute/filters.py
Signature: filter_attributes_by_product_types(qs, field, value, requestor, channel_slug) -> QuerySet
Description: Filters an attribute queryset to return only attributes belonging to product types that have products in a given category or collection. The function must return a deduplicated queryset — each attribute must appear at most once, even when multiple product types in the specified category or collection share the same attribute. The 'field' parameter indicates whether filtering is by category or collection. This function is invoked by both the legacy filter path and the newer where-clause path of the attributes GraphQL query.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.