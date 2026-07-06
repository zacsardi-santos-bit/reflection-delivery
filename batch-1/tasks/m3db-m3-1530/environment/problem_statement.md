## Description

The postings list cache in the index storage layer currently supports caching query results for two query types: regular expression queries and exact term queries. However, there is a third distinct query type — a field-existence query that checks whether a given field is present in the index at all, without matching against any specific value. These field-existence queries have no pattern, only a field name.

Currently, results from field-existence queries are never cached: every call to check field existence goes directly to the underlying segment. This means the cache provides no benefit for workloads that frequently query whether a particular field exists, forcing repeated expensive segment lookups for data that does not change.

## Expected Behavior

- The cache should support storing and retrieving postings lists for field-existence queries, keyed by segment UUID and field name.
- The read-through segment should check the cache before querying the underlying segment for field-existence results, and populate the cache on a miss.
- Cache disabling options that already control other query types should also apply to field-existence queries (when caching is disabled, all calls go directly to the segment).
- Running with no cache at all should still work correctly for field-existence queries.
- Field-existence entries should participate in LRU eviction and segment purging the same way other cached query types do.

## Why This Matters

Field-existence queries are a common access pattern in index lookups. Without caching, every repeated field check hits the segment unnecessarily. Adding caching for this query type brings it in line with the existing regexp and term caching, reducing redundant I/O and improving query performance.
