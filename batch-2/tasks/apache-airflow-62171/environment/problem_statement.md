## Description

The OpenLineage integration in Airflow currently tracks lineage at the task level — recording which datasets were read or written per task. However, when a SQL hook executes multiple individual queries during a task run, there is no way to emit separate per-query lineage events or associate each query with metadata such as external system query IDs. Additionally, there is no mechanism for merging two sets of lineage information together when both an operator-level extractor and hook-level lineage collection contribute results for the same task.

## Expected Behavior

- When SQL hooks collect execution metadata (SQL text and/or external query/job IDs) during a task run, the OpenLineage integration should emit a separate pair of lineage events (one for start and one for completion or failure) for each individual query, linked back to the parent task.
- Each per-query event should include the executed SQL text, any available external query identifier, and the namespace of the data source.
- A successful task state should produce completion events; a failed state should produce failure events.
- If SQL parsing fails, the integration should fall back to emitting events with only the raw SQL text as a facet.
- If event emission itself fails, the error should be logged as a warning rather than propagating to the caller.
- The lineage data container should support a merge operation that combines two results by concatenating inputs and outputs and merging metadata facets, where the current object's values take priority over the merged-in object's in case of conflicts.
- The function that produces lineage from SQL should support an option to skip establishing a live database connection, allowing it to be used in contexts where an active connection is not available or not desired.

## Why This Matters

SQL hooks often execute many individual queries per task, and some operators act as a "black box" capable of running arbitrary code — there is no visibility into what SQL was executed inside them unless hook-level lineage tracking is used. This change makes it possible to trace each query individually in the lineage graph, including the query text, the source system, and the success or failure of that specific query, giving data engineers much better observability into what happened during a task run.
