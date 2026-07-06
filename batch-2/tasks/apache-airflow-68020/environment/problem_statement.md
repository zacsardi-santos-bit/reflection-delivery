## Description

Every time Airflow checks which team owns a DAG — for example, during authorization checks on API endpoints — it makes a database query to look up the DAG's bundle and resolve the associated team name. Since team assignments rarely change, this repeated querying is wasteful. High-frequency endpoints (like the grid view, which polls continuously) end up hammering the database with redundant team-resolution queries for the same DAGs over and over.

## Expected Behavior

- The result of a DAG's team name lookup should be cached in memory after the first resolution, so that subsequent lookups for the same DAG skip the database entirely.
- The cache should be keyed by DAG ID so different DAGs are cached independently.
- A function should be provided to explicitly clear the entire cache, allowing fresh lookups to be forced when needed (e.g., after a team reassignment or during test isolation).
- Bulk operations over task instances should benefit from this caching by performing fewer total database queries — specifically, the overhead for bulk task instance deletion should drop by one query.

## Why This Matters

Without caching, every authorization check on a DAG causes a database join against the team tables. In busy deployments with many concurrent API requests or continuously polling UI components, this creates unnecessary database load. Caching the team name per DAG significantly reduces this pressure and improves response throughput for team-aware authorization checks.
