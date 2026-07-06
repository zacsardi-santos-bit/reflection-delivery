I'm hitting a bug in ClickHouse when I try to write an INSERT that reads client data through a common table expression and then references that CTE more than once — for instance, joining two sub-selects from the same CTE.

*   When a query references a client-data streaming table function through a CTE and that CTE is used more than once in the same query (e.g., via a CROSS JOIN between two CTE aliases derived from the same base CTE), the query must be rejected with error code INVALID_USAGE_OF_INPUT, not LOGICAL_ERROR.

*   The INVALID_USAGE_OF_INPUT rejection must apply to both the HTTP streaming path and the clickhouse-local callback path.

*   A query that wraps the streaming table function in a single CTE and reads from that CTE exactly once must succeed without error.

*   A query that uses the streaming table function directly (without any CTE wrapper) must succeed without error.

*   When SETTINGS enable_analyzer = 1 and enable_materialized_cte = 1 are active and the CTE is declared as MATERIALIZED, a query referencing the CTE multiple times (e.g., via CROSS JOIN) must succeed without error.

*   After a query is rejected with INVALID_USAGE_OF_INPUT, the server must remain alive and able to process subsequent queries (no crash or LOGICAL_ERROR abort in debug builds).

*   Rows inserted by valid queries (single-reference CTE, direct usage, and MATERIALIZED CTE workaround) must be persisted and returned correctly by subsequent SELECT queries.


*   Interface details: Type: Method
Name: initializePipeline
Location: src/Storages/StorageInput.cpp
Signature: void ReadFromInput::initializePipeline(QueryPipelineBuilder & pipeline, const BuildQueryPipelineSettings &)
Description: Initializes the query pipeline for reading from the client-provided input stream. Must include a one-shot guard at the top that throws INVALID_USAGE_OF_INPUT (not LOGICAL_ERROR) when the stream has already been consumed, covering both the HTTP shared-pipe path and the callback-backed path used by clickhouse-local. The guard must fire before any other pipeline initialization so that a second CTE reference is rejected immediately.

Type: Field
Name: was_pipe_used
Location: src/Storages/StorageInput.h (or StorageInput.cpp)
Description: Boolean flag on StorageInput (or accessible from ReadFromInput) that tracks whether the input stream has already been consumed. Must be set to true after the first successful pipeline initialization on any path (callback path or HTTP pipe path), so that a second call to initializePipeline detects the conflict.

Note: The error thrown for a second read attempt must use error code INVALID_USAGE_OF_INPUT. The LOGICAL_ERROR code must no longer be used for this scenario. The error message should explain that the streaming source is a one-shot stream and describe the MATERIALIZED CTE workaround with the enable_materialized_cte setting.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.