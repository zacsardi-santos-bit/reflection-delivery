## Description

The GraphQL API currently supports only cursor-based (keyset) pagination for browsing collections. While this is efficient for sequential traversal, it makes it difficult to implement page-number-style navigation or skip ahead to a specific position within a result set. Users need a way to specify a numeric offset to skip a certain number of records when querying a collection.

## Expected Behavior

- Collection queries should accept an optional integer argument that skips a specified number of records from the start or from an existing cursor position.
- This offset-style argument should only work with forward pagination — combining it with backward pagination arguments should return a descriptive error.
- When records are skipped by a non-zero offset, the pagination metadata should correctly reflect that there are previous pages available.
- When the offset is zero, pagination metadata should behave as if no offset was provided.
- The argument should be visible in schema introspection with a clear description indicating it is for forward pagination only and is an alternative to pure cursor-based pagination.

## Why This Matters

Offset-based pagination is a common and familiar pattern for many applications. Having access to it — even alongside cursor pagination — allows developers more flexibility in how they navigate and display paginated data from their database-backed GraphQL API.
