Implement support for cross-collection vector lookup in Qdrant's general-purpose query endpoint. Ensure proper access control and validation to prevent unauthorized access and invalid query combinations.

Requirements:

*   Update the POST /collections/{collection_name}/points/query endpoint:
    *   Support a top-level 'lookup_from' field to specify another collection for reference vector lookup, returning results equivalent to the dedicated recommend endpoint.
    *   Support 'lookup_from' in nested prefetch queries to look up vectors from another collection, behaving as if using actual vector values directly.
*   Error handling:
    *   Return a non-OK response with error message 'Not found: No point with id {id} found' if the 'lookup_from' field references a non-existent point ID.
    *   Return a non-OK response with error message 'Not found: Collection {collection_name} not found' if the 'lookup_from' field references a non-existent collection.
    *   Return a non-OK response with error message 'Wrong input: Not existing vector name error: {vector_name}' if the 'lookup_from' field references a non-existent vector name.
    *   Ensure these error behaviors apply to both top-level and nested prefetch queries.
*   Validation:
    *   Reject a fusion query combined with the 'using' field by returning a non-OK response with error message: 'Bad request: Fusion queries cannot be combined with the 'using' field.'
*   Access control:
    *   If a JWT token grants access only to specific collections, return HTTP 403 with error 'Forbidden: Access to collection {unauthorized_collection_name} is required' when:
        *   A top-level 'lookup_from' points to an unauthorized collection.
        *   A nested prefetch 'lookup_from' points to an unauthorized collection.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.