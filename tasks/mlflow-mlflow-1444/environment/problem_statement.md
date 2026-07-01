## Description

MLflow's run search API currently returns all matching runs in a single response. When experiments contain many runs, this makes it impossible to retrieve results in manageable chunks. There is no way for callers to request a specific page of results or to continue from where a previous search left off.

## Expected Behavior

- Run searches should return a result object that behaves like a list (individual runs accessible by index) and also carries a pagination token that callers can inspect.
- When more results are available, the token can be passed to a subsequent search call to retrieve the next page.
- The high-level client interface should accept an optional pagination token to pass through to the underlying store.
- Stores that do not yet support pagination should return a null token in their results (indicating no further pages), and should raise an error if a non-empty pagination token is supplied by the caller. Passing an empty-string token to such stores should succeed without error.
- Backend store implementations should implement a private internal method (rather than the existing public method) to handle the actual search-and-paginate logic, returning both the list of runs and the next-page token as a pair.

## Why This Matters

Without pagination, large experiments with many runs can produce unwieldy single responses, making it difficult to build efficient UIs or iterative workflows. Adding pagination support enables callers to retrieve results in pages, and provides a clear extension point for stores to implement full pagination incrementally.
