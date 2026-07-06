## Description

The cluster-wide storage statistics aggregation system needs a dedicated component for aggregating stats that come from the MySQL-backed stats store. Currently, the aggregation logic is coupled to the older generic snapshot format and cannot work directly with the structured, typed storage stats objects used by the MySQL store. Additionally, the partition class storage stats object cannot be instantiated without explicitly providing a data map, which causes errors whenever code needs to create an empty instance.

## Expected Behavior

- A new cluster aggregator class should be able to accept per-host storage stats from multiple cluster instances and produce two aggregated results:
  - A **raw combined** view: the sum of all storage stats from every instance in the cluster.
  - A **valid selected** view: the best replica's stats selected per partition, based on how recent and how large the reported usage is.
- When selecting the best replica for a partition, a replica with larger physical storage usage should be preferred if both replicas reported within a close time window. If one replica's report is significantly more recent than another's, the more recent report should be used regardless of size. Stale (outdated) replicas should be excluded from the valid view.
- Aggregation must work both for account-level storage stats and for partition class storage stats.
- The partition class storage stats object should be constructable without arguments, initializing to an empty state, and should also support being copied from an existing instance via a deep copy.

## Why This Matters

Without these capabilities, the MySQL-backed stats aggregation path cannot produce the structured aggregated results that downstream consumers expect, and any code path that needs to create an empty partition class stats object will fail with a null-related error.
