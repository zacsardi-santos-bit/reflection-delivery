## Description

The OpenLineage integration in Airflow currently only captures lineage automatically through internal listeners attached to the task lifecycle. There is no stable public API that developers can call directly from within their custom task code to emit lineage events for datasets they read or write, or for SQL queries they execute. Without such an API, operators that manage their own connections or execute queries outside of Airflow's built-in SQL operators have no reliable way to contribute lineage data.

## Expected Behavior

- A new public API module should be available that lets developers check whether OpenLineage is active and emit arbitrary events to the configured backend.
- A dedicated function for emitting dataset lineage should be available. When called from within a running task, it should automatically populate all standard Airflow metadata — run IDs, parent-child run relationships, ownership, tags, and timing information — and emit a single lineage event associating the task with its input and output datasets.
- A dedicated function for emitting SQL query lineage should be available. It should emit a start/end event pair for each query, automatically numbering queries within a task run, attaching query identity metadata, and optionally parsing the SQL text to extract dataset-level lineage.
- Both functions should resolve the running task context automatically when no task instance is explicitly passed.
- Errors during emission should be swallowed by default and logged as warnings, so that lineage failures never disrupt task execution.
- Shared helper utilities for composing run and job facets should be extracted from the listener internals into reusable, independently testable functions.

## Why This Matters

Without a public API, custom operators cannot contribute lineage data unless they replicate internal implementation details. A stable, high-level API makes it straightforward for any task author to integrate lineage tracking without deep knowledge of the underlying listener infrastructure.
