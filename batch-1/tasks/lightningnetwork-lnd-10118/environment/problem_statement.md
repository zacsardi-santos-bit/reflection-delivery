## Description

The SQL query helpers in our database layer support batching large input slices into smaller chunks for IN-clause queries, but there's no shared utility for cursor-based pagination — the pattern where you repeatedly fetch pages of results by advancing a position marker until all records have been returned.

Currently, any code that needs to iterate over large result sets using cursor-based pagination must duplicate the looping, cursor-advance, and error-handling logic manually. This makes the code harder to maintain and leaves room for subtle bugs.

## Expected Behavior

- A new general-purpose helper should handle cursor-based pagination: given an initial cursor, a per-page fetch function, a cursor-extraction function, and a per-item processing callback, it should automatically advance through pages until results are exhausted or an error occurs.
- The helper should stop fetching additional pages as soon as a page returns fewer items than the configured maximum page size (indicating the last page has been reached).
- Errors from the fetch function should be propagated with context indicating which cursor position failed.
- Errors from item processing should be propagated immediately, stopping further processing.
- The existing batch query configuration type and related functions should be renamed to better reflect their purpose and to clearly distinguish them from the new cursor-based pagination support. The configuration type should be unified to cover both batch and paginated queries.

## Why This Matters

Without a shared cursor-pagination helper, multiple parts of the codebase must each maintain their own pagination loops. A unified helper reduces duplication, makes behavior consistent, and makes it easier to add new paginated queries in the future.
