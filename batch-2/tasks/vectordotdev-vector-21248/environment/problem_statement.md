## Description

Vector currently has no built-in way to send observability events (logs, metrics, or traces) directly to a PostgreSQL database. This is a common use case for teams who want to store event data in their own Postgres tables for analytics, auditing, or long-term retention.

We need a new PostgreSQL sink component that allows Vector to write batches of events into a user-specified database table. The sink should accept a connection string and a table name as configuration, and support connection pooling to handle concurrent writes efficiently.

## Expected Behavior

- Users should be able to configure the sink with a PostgreSQL connection string and a destination table name
- The configuration should be parseable from a TOML config file and support Vector's standard configuration generation mechanism
- The sink should accept log, metric, and trace events
- Batching and request retry behavior should be configurable using Vector's standard settings
- Connection pooling should be supported to improve performance

## Why This Matters

Many organizations already use PostgreSQL as a primary data store and would benefit from being able to route observability data there directly from Vector without needing a separate ETL pipeline. Adding native Postgres support closes this gap and makes Vector a first-class option for Postgres-based observability workflows.
