## Description

TencentVectorDB is a fully managed enterprise vector database service, but it currently lacks support for LangChain's self-querying retrieval system. This means developers cannot use natural language queries that automatically translate into structured metadata filters — they have to write raw filter expressions by hand, which is error-prone and prevents use of LangChain's higher-level query construction tools.

## Expected Behavior

- A translator component should be available for TencentVectorDB that converts LangChain's structured query format (with logical operators like AND and OR, and comparisons like equality, less-than, and set membership) into the raw filter expression syntax that TencentVectorDB accepts.
- Logical OR expressions should be wrapped in parentheses in the output expression, while AND expressions should join their sub-expressions directly.
- The translator should optionally accept a list of allowed metadata field names. When such a list is provided and a query references a field not in the list, the system should raise an error clearly identifying the unsupported field name.
- A utility function should also be available to translate LangChain filter strings directly into TencentVectorDB expressions, for use within the vector store itself.
- TencentVectorDB should be registered as a compatible store for LangChain's indexing functionality.

## Why This Matters

Without this integration, TencentVectorDB users cannot use LangChain's self-query retrieval pipeline. Adding this support brings TencentVectorDB in line with other vector stores that already support self-querying, and allows developers to build more sophisticated retrieval applications with automatic metadata filtering.
