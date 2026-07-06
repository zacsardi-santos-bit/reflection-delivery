# Vector Store: Add Filtering, CRUD Operations, and Timestamp Explosion

## Description

The current vector store abstraction only supports basic similarity search. There is no way to filter results by document metadata, no support for inserting, updating, or removing individual documents, and no structured handling of date/time fields. This makes it impossible to build queries like "find the top 5 most similar documents where the category is 'feature' and the priority is greater than 1" without post-processing in application code.

We also need a composable, serializable filter expression system — one that can be built programmatically and passed to search calls — rather than raw query strings tied to a specific backend.

## Expected Behavior

- A filter expression system that supports equality, inequality, range comparisons (greater-than, less-than, etc.), and membership checks on named fields
- Logical combinations of filters using AND, OR, and NOT, with chaining that produces flat (not nested) expressions
- Filter expressions must support client-side evaluation against a record dict and must survive JSON serialization/deserialization round-trips
- Vector store implementations must accept a filter expression as an optional parameter on similarity search methods, restricting results to matching documents while preserving similarity ordering
- A field projection option on search methods to limit which metadata fields are returned per document
- An option to omit vectors from search results when they are not needed
- Individual document lifecycle operations: single-document insert, update, remove by id, and total document count
- Searching for a non-existent document by id must raise an error rather than returning a silent placeholder
- Date fields must be automatically decomposed into structured components (year, month, month name, day, day of week, hour, quarter) and stored alongside the document so they can be used in filter expressions
- The built-in creation and modification timestamp fields should be automatically populated and also decomposed into filterable components

## Why This Matters

Without metadata filtering, every vector search returns results that must be manually post-filtered in application code. A proper filter system, combined with structured timestamp decomposition, enables rich time-aware, metadata-scoped vector queries directly in the store layer. The added CRUD operations make it feasible to maintain document collections incrementally without workarounds.
