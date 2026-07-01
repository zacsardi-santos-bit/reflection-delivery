## Add Job Listing Capability to the Database Adapter

## Description

Currently the job queue library has no way to query and inspect jobs beyond simply picking them up for execution. Operators and developers cannot retrieve lists of jobs filtered by state, queue, or metadata without writing raw SQL queries directly against the database. This makes it difficult to build dashboards, auditing tools, or any functionality that needs to observe the current state of the job queue.

## Expected Behavior

- A new function should be available at the low-level database layer to retrieve jobs from the database with flexible filtering.
- Filtering must support: job state (or no state filter at all), queue name(s), arbitrary additional SQL conditions with named parameters, and a configurable sort order with one or more ORDER BY expressions.
- A result limit must always be specified.
- The same query must be executable both on a direct database connection and inside a caller-supplied transaction.
- The higher-level database adapter interface must expose both variants so that all adapter implementations (real and test) are required to provide them.

## Why This Matters

Without job listing, the only way to inspect what jobs are queued, running, or completed is to run raw SQL. Formalizing this as part of the adapter API means it can be used safely with proper parameter binding, state-based filtering, and pagination — and any mock adapters used in tests will automatically need to implement the same contract.
