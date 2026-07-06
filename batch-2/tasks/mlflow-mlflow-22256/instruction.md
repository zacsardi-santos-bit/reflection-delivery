I'm working on adding a binary archival format for MLflow trace data.

*   The TRACE_ARCHIVAL_FILENAME constant in mlflow/tracing/otel/otel_archival.py must equal the string "traces.pb".

*   spans_to_traces_data_pb must raise MlflowException with a message matching "at least one span" when given an empty span list.

*   spans_to_traces_data_pb must raise MlflowException with a message matching "distinct trace IDs" when the input spans carry more than one distinct OTLP trace ID.

*   spans_to_traces_data_pb must raise MlflowException with a message matching "same OTLP resource" when input spans have different (non-equivalent) OTLP resource objects.

*   spans_to_traces_data_pb must treat two resource objects with the same attributes in different key order as equivalent (no error raised).

*   spans_to_traces_data_pb must preserve resource attributes in the serialized OTLP protobuf output so that they are accessible after deserialization.

*   traces_data_pb_to_spans must raise MlflowException with a message matching "non-empty OTLP TracesData protobuf" when given an empty or zero-length byte string.

*   traces_data_pb_to_spans must raise MlflowException with a message matching "valid OTLP TracesData protobuf" when given bytes that cannot be parsed as a valid protobuf.

*   traces_data_pb_to_spans must raise MlflowException with a message matching "exactly one ResourceSpans group" when the deserialized protobuf contains more than one ResourceSpans entry.

*   traces_data_pb_to_spans must raise MlflowException with a message matching "exactly one ScopeSpans group" when the single ResourceSpans entry contains more than one ScopeSpans entry.

*   traces_data_pb_to_spans must raise MlflowException with a message matching "distinct trace IDs" when the deserialized spans carry more than one distinct OTLP trace ID.

*   traces_data_pb_to_spans must raise MlflowException with a message matching "contain at least one span" when the protobuf payload is structurally valid but contains no spans.

*   spans_to_traces_data_pb followed by traces_data_pb_to_spans must round-trip a list of spans (single or multiple from the same trace) such that each span's dict representation is preserved.

*   LocalArtifactRepository.upload_archived_trace_data must accept either a JSON-serialized string of TraceData or a TraceData object directly, serialize the spans to OTLP protobuf, and write the result to a file named TRACE_ARCHIVAL_FILENAME inside the artifact directory. It must raise MlflowException matching "at least one span" when the span list is empty.

*   LocalArtifactRepository.upload_archived_trace_data_bytes must write raw protobuf bytes directly to a file named TRACE_ARCHIVAL_FILENAME inside the artifact directory without re-serializing.

*   LocalArtifactRepository.download_archived_trace_data must read TRACE_ARCHIVAL_FILENAME and return a TraceData object. It must raise MlflowTraceDataNotFound with a message matching "Trace data not found for path=" when the file does not exist, and MlflowTraceDataCorrupted with a message matching "Trace data is corrupted for path=" when the file is present but its contents cannot be deserialized.

*   S3ArtifactRepository.upload_archived_trace_data_bytes must upload the provided bytes to S3 using the S3 client's upload_fileobj method, with ContentType set to "application/octet-stream", using the repository bucket and a key composed of the artifact path joined with TRACE_ARCHIVAL_FILENAME.

*   S3ArtifactRepository.upload_archived_trace_data must serialize the TraceData (or JSON string) to OTLP protobuf bytes and then upload via upload_archived_trace_data_bytes.

*   S3ArtifactRepository.download_archived_trace_data must download TRACE_ARCHIVAL_FILENAME from the repository bucket and deserialize it to a TraceData object that matches the originally uploaded data.

*   DatabricksArtifactRepository.download_archived_trace_data, upload_archived_trace_data, and upload_archived_trace_data_bytes must each raise MlflowException with a message matching "do not yet support ARCHIVE_REPO".


*   Interface details: Type: Constant
Name: TRACE_ARCHIVAL_FILENAME
Location: mlflow/tracing/otel/otel_archival.py
Description: Filename used when storing archived trace data in artifact repositories. Must equal the string "traces.pb".

Type: Function
Name: spans_to_traces_data_pb
Location: mlflow/tracing/otel/otel_archival.py
Signature: spans_to_traces_data_pb(spans: list[Span]) -> bytes
Description: Serializes a list of Span objects to OTLP TracesData protobuf bytes. Raises MlflowException (matching "at least one span") if the list is empty. Raises MlflowException (matching "distinct trace IDs") if spans belong to more than one OTLP trace ID. Raises MlflowException (matching "same OTLP resource") if spans have different OTLP resource objects. Accepts spans whose resources have the same attributes in different ordering. Preserves resource attributes in the serialized protobuf output.

Type: Function
Name: traces_data_pb_to_spans
Location: mlflow/tracing/otel/otel_archival.py
Signature: traces_data_pb_to_spans(data: bytes) -> list[Span]
Description: Deserializes OTLP TracesData protobuf bytes back to a list of Span objects. Raises MlflowException (matching "non-empty OTLP TracesData protobuf") for empty/zero-byte input. Raises MlflowException (matching "valid OTLP TracesData protobuf") if the bytes are not a valid protobuf. Raises MlflowException (matching "exactly one ResourceSpans group") if the protobuf contains multiple ResourceSpans groups. Raises MlflowException (matching "exactly one ScopeSpans group") if the protobuf contains multiple ScopeSpans groups. Raises MlflowException (matching "distinct trace IDs") if spans carry different OTLP trace IDs. Raises MlflowException (matching "contain at least one span") if the payload has no spans.

Type: Class
Name: LocalArtifactRepository
Location: mlflow/store/artifact/local_artifact_repo.py
Description: Existing class that must be extended with three new methods.
Signature:
  upload_archived_trace_data(data: str | TraceData) -> None
    Accepts either a serialized JSON string of TraceData or a TraceData object. Raises MlflowException (matching "at least one span") if the span list is empty. Writes the serialized protobuf to TRACE_ARCHIVAL_FILENAME in the artifact directory.
  upload_archived_trace_data_bytes(data: bytes) -> None
    Writes raw protobuf bytes directly to TRACE_ARCHIVAL_FILENAME in the artifact directory.
  download_archived_trace_data() -> TraceData
    Reads TRACE_ARCHIVAL_FILENAME from the artifact directory and returns a TraceData object. Raises MlflowTraceDataNotFound (matching "Trace data not found for path=") if the file does not exist. Raises MlflowTraceDataCorrupted (matching "Trace data is corrupted for path=") if the file contents are invalid/corrupt.

Type: Class
Name: S3ArtifactRepository
Location: mlflow/store/artifact/s3_artifact_repo.py
Description: Existing class that must be extended with three new methods.
Signature:
  upload_archived_trace_data_bytes(data: bytes) -> None
    Uploads bytes to S3 using the S3 client's upload_fileobj method with ContentType="application/octet-stream". The S3 key must be <artifact_path>/<TRACE_ARCHIVAL_FILENAME> and the bucket must be the repository bucket.
  upload_archived_trace_data(trace_data: TraceData | str) -> None
    Serializes trace_data via spans_to_traces_data_pb and uploads using upload_archived_trace_data_bytes.
  download_archived_trace_data() -> TraceData
    Downloads TRACE_ARCHIVAL_FILENAME from S3 and deserializes it to a TraceData object.

Type: Class
Name: DatabricksArtifactRepository
Location: mlflow/store/artifact/databricks_artifact_repo.py
Description: Existing class that must be extended with three new stub methods, each raising MlflowException.
Signature:
  download_archived_trace_data() -> None
    Raises MlflowException with a message matching "do not yet support ARCHIVE_REPO".
  upload_archived_trace_data(data) -> None
    Raises MlflowException with a message matching "do not yet support ARCHIVE_REPO".
  upload_archived_trace_data_bytes(data: bytes) -> None
    Raises MlflowException with a message matching "do not yet support ARCHIVE_REPO".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.