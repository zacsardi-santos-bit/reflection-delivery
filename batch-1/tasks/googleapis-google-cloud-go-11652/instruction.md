Implement tracing enhancements in the Cloud Spanner Go client library to include contextual attributes in distributed tracing spans. Introduce utility functions to standardize span creation and attribute extraction from RPC requests, ensuring minimal overhead when tracing is not enabled.

*   Implement the `startSpan` function in `spanner/trace.go`:
    *   Create an OpenTelemetry span with a name prefixed by the Spanner package path.
    *   Apply provided span start options.
    *   Use a tracer with InstrumentationLibrary Name "cloud.google.com/go/spanner" and Version `internal.Version`.
    *   Return the updated context and the new span.

*   Implement the `endSpan` function in `spanner/trace.go`:
    *   Retrieve and end the current span from the context.
    *   If an error is provided, record it as a span event and set the span status to Error.

*   Implement the `prependPackageName` function in `spanner/trace.go`:
    *   Return a string formatted as "cloud.google.com/go/spanner.<spanName>".

*   Define constants in `spanner/trace.go`:
    *   `gcpClientRepo`: "googleapis/google-cloud-go".
    *   `gcpClientArtifact`: "cloud.google.com/go/spanner".

*   Implement the `setSpanAttributes` function in `spanner/grpc_client.go`:
    *   Accept an OpenTelemetry span and any Spanner RPC request.
    *   Return immediately if the span is not recording.
    *   For requests with `RequestOptions`, set "transaction.tag" and "statement.tag" attributes if non-empty.
    *   For requests with a SQL string (`GetSql`), set "db.statement" as a string attribute.
    *   For batch DML requests (`GetStatements`), set "db.statement" as a string-slice attribute.

*   Ensure spans for `ReadWriteTransactionWithOptions` operations include:
    *   Span name: "cloud.google.com/go/spanner.ReadWriteTransactionWithOptions".
    *   Attributes: "db.name", "instance.name", "cloud.region", "gcp.client.version", "gcp.client.repo", "gcp.client.artifact", "transaction.tag".

*   Ensure spans for `RowIterator` operations include:
    *   Span name: "cloud.google.com/go/spanner.RowIterator".
    *   Attributes: "statement.tag", "db.statement", "transaction.tag" when applicable.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.