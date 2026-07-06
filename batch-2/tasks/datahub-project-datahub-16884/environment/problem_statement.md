## Description

The Teradata ingestion connector is slow and unreliable for large Teradata environments. Several performance and correctness issues make it impractical to run frequently or against environments with many databases.

## Issues

- **No incremental column extraction**: Every run re-fetches column metadata for all tables, even ones that haven't changed since the last run. There should be a way to supply a watermark (either as an explicit timestamp or as a "days back" window computed from the server clock) so that tables not modified since the watermark can be skipped.
- **Slow view column extraction**: View column information is always retrieved using a slow, per-table query. A faster bulk lookup path should be used when column type information is available, with automatic fallback to the slow path for derived columns whose type cannot be determined from the catalog.
- **Schema cache too small**: The LRU caches for schema-level metadata have a cap that is too low for environments with many databases. Entries are silently evicted and the same data is re-fetched repeatedly. The cache size should be raised to accommodate a realistic number of active schemas.
- **Lineage queries not scoped to discovered databases**: Lineage queries scan all databases rather than only the ones discovered during table scanning. They should be automatically scoped to discovered databases (excluding internal system databases), falling back to no filter when no tables have been cached.
- **Case-sensitive database name filtering**: Database name comparisons in lineage queries are case-sensitive. Environments that use mixed-case database names see missed matches. Comparisons should be performed in a case-insensitive manner.
- **Hardcoded connection timeouts**: Request and connection timeout values are hardcoded and cannot be tuned per deployment. They should be configurable.

## Expected Behavior

- A new configuration option allows specifying a watermark timestamp (or a number of days back relative to the server clock) to limit column extraction to recently changed tables.
- A new configuration flag enables faster bulk column fetching for views, with fallback to the slower individual query when a column has no determinable type.
- Schema caches should retain entries across at least 32 distinct schemas without eviction.
- Lineage queries should filter to discovered databases, respect the database allow-list, exclude system databases, and omit the filter entirely when no databases are cached.
- Database name comparisons in lineage queries should be case-insensitive.
- Request and connection timeout millisecond values should be configurable, with sensible defaults.

## Why This Matters

Large Teradata deployments can have hundreds of databases and thousands of tables. Without these improvements, each run is slow, produces incomplete lineage, and is brittle in environments that mix database name casing conventions.
