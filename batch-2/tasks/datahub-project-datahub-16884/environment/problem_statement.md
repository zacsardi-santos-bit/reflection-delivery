I'm digging into the Teradata ingestion connector in DataHub and it's painful on big environments, hundreds of databases, thousands of tables, so I need a few perf and correctness fixes.

First, it re-fetches column metadata for every table on every run even when nothing changed. I want an incremental extraction mode where I can hand it an explicit timestamp watermark or just say "look back N days" computed off the Teradata server's clock (not the client clock). Tables not modified since the watermark should get skipped. Oh and if the watermark value is timezone-aware, normalize it to naive UTC before comparing, since Teradata hands back timestamps with no tz info.

For views, it always uses that slow per-table query to get column info. I'd like a config flag to use a faster bulk catalog lookup instead, but fall back to the slow path automatically for any view where a column's type is missing or just whitespace.

The LRU caches for schema-level metadata are way too small, bump them to 32 entries so queries across lots of databases don't keep evicting and re-fetching the same stuff (should hold at least 32 distinct schemas without eviction).

Lineage queries don't scope to the databases we actually discovered during table scanning. They should filter to just those discovered databases minus system/excluded ones, respect the configured database allow-list, and skip the filter entirely when the table cache is empty. Also the database name comparisons in these queries need to be case-insensitive using Teradata's case-insensitive syntax, since mixed-case naming causes missed matches right now.

Last thing, connection and request timeouts are hardcoded. Expose them as config options in milliseconds, defaulting to 120 seconds for the request timeout and 30 seconds for the connection timeout, so slow environments can tune without touching source.

Relevant code lives under the Teradata connector source in `@metadata-ingestion/src/datahub/ingestion/source/sql/teradata.py`. Without this each run is slow, produces incomplete lineage, and breaks in casing-mixed environments.
