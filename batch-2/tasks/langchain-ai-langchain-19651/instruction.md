Implement a translator for TencentVectorDB that converts LangChain's structured query format into the filter expression syntax required by TencentVectorDB. Ensure the translator supports logical operators and comparisons, and optionally restricts queries to allowed metadata fields.

*   Implement the `translate_filter` function in `libs/community/langchain_community/vectorstores/tencentvectordb.py`:
    *   Accept a LangChain query constructor filter string and an optional list of allowed fields.
    *   Return a TencentVectorDB expression string.
    *   Translate 'and' and 'or' logical operators, wrapping 'or' expressions in parentheses.
    *   Convert 'eq', 'lt', and 'in' comparisons into appropriate TencentVectorDB syntax.
    *   Raise a `ValueError` with the message 'Expr Filtering found Unsupported attribute: {attribute}' if a field not in the allowed list is used.

*   Implement the `TencentVectorDBTranslator` class in `libs/langchain/langchain/retrievers/self_query/tencentvectordb.py`:
    *   Accept an optional `meta_keys` parameter in the constructor to specify allowed metadata fields.
    *   Implement the `visit_structured_query` method:
        *   Return a tuple `(query_string, kwargs_dict)`, with `kwargs_dict` containing an 'expr' key for the translated filter.
        *   Translate logical operators and comparisons as specified, ensuring correct syntax and parentheses usage.
        *   Raise a `ValueError` for unsupported fields when `meta_keys` is set.

*   Ensure `TencentVectorDB` is registered as a compatible vector store:
    *   Make `TencentVectorDB` importable from `langchain_community.vectorstores`.
    *   List `TencentVectorDB` as a compatible store in the indexing documentation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.