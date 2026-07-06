Implement a consistent serialization and deserialization system for MLflow's tracing system to ensure traces can be round-tripped between dictionary/JSON formats and Trace objects. Update the MlflowLangchainTracer to handle structured traces correctly and manage span events and errors effectively.

*   Implement the Trace class in `mlflow/entities/trace.py` with the following methods:
    *   `to_dict() -> dict`: Serialize the trace to a dictionary.
    *   `from_dict(cls, d: dict) -> Trace`: Reconstruct a Trace object from a dictionary.
    *   `to_json() -> str`: Serialize the trace to a JSON string.
    *   `from_json(cls, s: str) -> Trace`: Reconstruct a Trace object from a JSON string.
*   Ensure the reconstructed trace maintains equality with the original trace in terms of info, data.request, data.response, and data.spans.
*   Modify `pop_trace(request_id)` in `mlflow/tracing/export/inference_table.py` to return a dictionary with 'info' and 'data' keys, compatible with `Trace.from_dict()`.
*   Store span attributes as dictionaries, not JSON strings, in serialized traces.
*   Store the schema version in trace info tags as a string '2'.
*   Log a warning when accessing span attributes with unsupported types using `mlflow.entities.span._logger.warning`.

*   Update the MlflowLangchainTracer class in `mlflow/langchain/langchain_tracer.py`:
    *   Accept an optional `prediction_context` argument in the constructor.
    *   Maintain a `_run_span_mapping` attribute mapping run_id strings to spans.
    *   Implement callback methods:
        *   `on_llm_start`, `on_llm_new_token`, `on_llm_end`, `on_llm_error`
            *   `on_llm_new_token`: Record a span event named "new_token".
            *   `on_llm_end`: Set span outputs, raise an exception if run_id not found.
            *   `on_llm_error`: Set span status to ERROR, record a span event.
        *   `on_retriever_start`, `on_retriever_end`, `on_retriever_error`
            *   `on_retriever_end`: Set span outputs, raise an exception if run_id not found.
            *   `on_retriever_error`: Set span status to ERROR, record a span event.
        *   `on_chain_start`, `on_chain_end`
    *   Ensure thread safety for concurrent chain executions.

*   Ensure SpanEvent objects are dataclass-compatible, with `dataclasses.asdict(event)` returning a dictionary with 'name', 'timestamp', and 'attributes'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.