## Description

The task instances listing endpoint currently supports only offset-based pagination, which counts all matching records on every request. For deployments with large numbers of task instances this total-count query is expensive and can significantly slow down every paginated response.

We need to add cursor-based pagination as an alternative mode. Instead of passing an offset, clients would supply an opaque cursor token obtained from a previous response, and the server would return tokens pointing to the next and previous pages without performing a full count.

## Expected Behavior

- The listing endpoint accepts an optional cursor parameter. When present, the response includes next-page and previous-page cursor tokens rather than a total count.
- An empty cursor value means "first page" of cursor-based pagination.
- Navigating forward and then backward through all pages must produce the same set of results in the same order.
- An invalid or malformed cursor token must be rejected with an HTTP 400 error.
- The response schema must always include cursor fields (set to null when not in cursor mode) so API consumers can handle the shape consistently.

## Why This Matters

Large Airflow deployments can have millions of task instances. Counting all matching records on every paginated request is slow and wasteful. Cursor-based pagination removes this bottleneck, enabling efficient and scalable traversal of large result sets in both forward and backward directions.
