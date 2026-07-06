Organize the MLflow tracing module by relocating span-related classes and constants to more intuitive locations, and ensure trace data deserialization is complete. Update the client method to return a fully usable trace object.

*   Relocate Span-related Classes:
    *   Move `NoOpSpan`, `Span`, and `LiveSpan` classes to `mlflow.entities`.
    *   Ensure these classes are importable from `mlflow.entities` and remove old import paths from `mlflow.tracing.types.wrapper`.

*   Relocate Tracing Constants:
    *   Move constants `SpanAttributeKey`, `TraceMetadataKey`, `TraceTagKey`, `MAX_CHARS_IN_TRACE_INFO_METADATA_AND_TAGS`, and `TRUNCATION_SUFFIX` to `mlflow.tracing.constant`.
    *   Ensure these constants are importable from `mlflow.tracing.constant` and remove old paths from `mlflow.tracing.types.constant`.

*   Complete Trace Data Deserialization:
    *   Update `TraceData.from_dict(d)` to reconstruct `request`, `response`, and `spans` fields.
    *   Ensure `TraceData.from_dict(trace_data.to_dict()).to_dict()` equals `trace_data.to_dict()` for lossless conversion.

*   Enhance Span Deserialization:
    *   Implement `Span.from_dict(data)` to construct a `Span` object with properties: `name`, `request_id`, `inputs`, `outputs`, `start_time_ns`, `end_time_ns`, and `status.status_code`.
    *   Raise `MlflowException` if `request_id` is empty or missing.

*   Update Client Trace Retrieval:
    *   Modify `MlflowClient.get_trace()` to return a fully populated `Trace` object.
    *   Ensure `trace.info` includes `request_id`, `experiment_id`, `timestamp_ms`, `execution_time_ms`, `status`, and `tags`.
    *   Ensure `trace.data` includes `request`, `response`, and `spans` with correct span details.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.