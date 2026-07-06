## Description

CockroachDB exposes a large number of metrics about its background jobs through its Prometheus monitoring endpoint, but the integration currently collects only a small fraction of them. Background jobs handle critical database operations such as backups, data imports, schema changes, data replication, automatic statistics collection, and TTL cleanup. Without visibility into these jobs, operators have no reliable way to detect when jobs are stuck, failing, or consuming unexpected resources.

## Expected Behavior

- The integration should collect metrics for all background job types, tracking the number of jobs that are currently idle, paused, or running.
- Completion and failure metrics should be collected for each job type, including counts for jobs that completed successfully, failed with non-retriable errors, or failed with retriable errors during both normal execution and failure/cancellation processing.
- Protected timestamp record metrics (count, age, expired records) should be collected per job type.
- A small set of changefeed job metrics that were previously missing from changefeed monitoring should now also be collected under the changefeed category.
- Global job registry metrics such as total claimed jobs and job adoption iteration counts should be collected.
- Row-level TTL job metrics including row counts, span counts, and per-operation durations should be collected.

## Why This Matters

Without these metrics, operators using standard monitoring dashboards cannot observe job health across their CockroachDB cluster. A backup job silently failing or a schema migration stalling would go undetected until manual inspection. Adding comprehensive job metric coverage enables alerting on job failures and capacity planning based on job activity trends.
