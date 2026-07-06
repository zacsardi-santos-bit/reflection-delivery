## Description

Attempting to retrieve Elasticsearch SQL query results as a Polars DataFrame currently fails immediately with a not-implemented error. The Elasticsearch provider's database hook does not support Polars because the standard database adapter interface is not fully compatible with Elasticsearch's query execution model.

## Expected Behavior

- Querying Elasticsearch using SQL and requesting results in Polars DataFrame format should succeed instead of raising an error.
- The implementation should use Elasticsearch's native cursor-based pagination to retrieve results in batches, handling large result sets efficiently.
- Users should be able to optionally limit the total number of rows returned.
- After retrieving paginated results, any open cursor resources should be properly cleaned up.
- When results fit on a single page and no cursor is used, no cleanup call should be made (since there is nothing to clean up).
- When a row limit is satisfied within the first page before pagination begins, cursor cleanup should likewise be skipped.

## Why This Matters

Polars is a popular high-performance DataFrame library, and other database backends in Airflow already support it. Elasticsearch is currently a second-class citizen in this regard — developers who want to work with Elasticsearch SQL results in Polars are forced to use workarounds. This change brings Elasticsearch up to parity with other supported databases for Polars DataFrame retrieval.
