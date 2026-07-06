Implement an imperative API for MLflow's tracing system to allow developers to manually start and end traces and spans using the MLflow client. Ensure that the system can integrate with external callback-based frameworks and maintain consistency in time units across the system.

*   Update the `InMemoryTraceManager` class in `mlflow/tracing/trace_manager.py`:
    *   Ensure it is a singleton, accessible via `get_instance()`.
    *   Maintain a `_traces` dictionary with each entry having a `span_dict` attribute.
    *   Implement `flush()` to clear all entries from `_traces`.
    *   Implement `add_or_update_span(span)` to add or update spans in `_traces`, ensuring thread safety.
    *   Implement `pop_trace(trace_id)` to remove and return a `Trace` object for the given `trace_id`, returning `None` if not found.
    *   Implement `start_detached_span(name, trace_id=None, parent_span_id=None)` to create and register a new `MLflowSpanWrapper`, creating a new trace if `trace_id` is `None`.
    *   Implement `get_span_from_id(trace_id, span_id)` to return the corresponding `MLflowSpanWrapper`.
    *   Implement `get_root_span_id(trace_id)` to return the `span_id` of the root span for the given trace.

*   Modify the `MLflowSpanWrapper` class in `mlflow/tracing/types/wrapper.py`:
    *   Ensure `start_time` and `end_time` properties return microsecond values by dividing OpenTelemetry nanosecond timestamps by 1000.

*   Update the `MLflowSpanExporter` class in `mlflow/tracing/export/mlflow.py`:
    *   Reference the `InMemoryTraceManager` singleton via `_trace_manager`.

*   Extend the `MlflowClient` class in `mlflow/tracking/client.py`:
    *   Implement `start_trace(name, inputs=None, tags=None)` to create a root span for a new trace.
    *   Implement `start_span(name, span_type=None, trace_id=None, parent_span_id=None, inputs=None)` to create a child span, raising an `MlflowException` if `parent_span_id` is `None`.
    *   Implement `end_span(trace_id, span_id, outputs=None, attributes=None)` to finalize a span.
    *   Implement `end_trace(trace_id, outputs=None, status=None)` to finalize the root span and export the complete trace, ensuring unfinished spans are included with `end_time=None` and `status.status_code=StatusCode.UNSET`.

*   Ensure all span timestamps are in microseconds and update any elapsed-time assertions accordingly.
*   Allow mixing of high-level context-manager APIs with the new imperative client APIs, ensuring all spans are correctly linked and appear in a single trace.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.