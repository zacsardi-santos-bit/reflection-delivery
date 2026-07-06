I'm working on the HTTP transport layer for a Google Cloud client library.

*   The otelAttributeTransport struct must have a logger field of type *slog.Logger so that a structured logger can be attached when constructing the transport directly.

*   A concrete type named errorTrackingBody must be defined in the httptransport package. It must implement io.ReadCloser so it can replace an http.Response.Body. Callers must be able to perform a *errorTrackingBody type assertion on the response body.

*   When the experimental logging feature flag (GOOGLE_SDK_GO_EXPERIMENTAL_LOGGING) is enabled and an HTTP response indicates an error, the transport must emit exactly one DEBUG-level structured JSON log record per request/response cycle.

*   The log record for an HTTP error response must include the following fields: msg (the error message string from the response body), rpc.system.name set to 'http', rpc.response.status_code (gRPC-style status string), http.response.status_code (numeric HTTP status code as a number), http.request.method, and error.type.

*   If the response body contains a google.rpc.ErrorInfo detail entry with a recognized actionable reason (such as RATE_LIMIT_EXCEEDED or IAM_PERMISSION_DENIED), error.type must be set to that reason string. If multiple ErrorInfo entries are present, the first one with an actionable reason must be used.

*   If the ErrorInfo entry includes a domain field, the log record must include gcp.errors.domain set to that domain. If the ErrorInfo entry includes a metadata map, each key-value pair must appear in the log record as gcp.errors.metadata.<key>.

*   If the request context carries a telemetry value for 'resend_count', the log record must include http.request.resend_count as a number. If the context carries 'resource_name', the log record must include gcp.resource.destination.id.

*   For a context.DeadlineExceeded transport error, the log record must set error.type to 'CLIENT_TIMEOUT' and rpc.response.status_code to 'DEADLINE_EXCEEDED'. For context.Canceled, error.type must be 'CLIENT_CANCELLED' and rpc.response.status_code must be 'CANCELED'. For any other error without an HTTP response, error.type must be the Go reflect type name of the error (e.g. '*errors.errorString') and rpc.response.status_code must be 'UNKNOWN'.

*   When the experimental logging feature flag is disabled, the transport must produce no log output whatsoever; the round trip must complete without any logging side effects.

*   The log record must NOT be emitted until the response body is closed. When the log is emitted on body close, any active OpenTelemetry span associated with the request must still be recording at that moment.

*   When a response body's ContentLength is greater than 8192 (8 KB), the transport must log immediately using the HTTP status string (e.g. '400 Bad Request') as the message, and must NOT wrap the body in an errorTrackingBody wrapper.

*   When a response body's actual content exceeds 8192 bytes (but ContentLength is unknown), the transport must fall back to the HTTP status string as the log message.

*   When the response body is closed before being fully read (early close), or when reading the body produces a network error, the transport must still emit a log record.

*   Logging and tracing must operate independently. Enabling only tracing must add telemetry span attributes (such as gcp.client.version) to spans but emit no log records. Enabling only logging must emit log records for error responses but must not add the telemetry attributes to spans. Enabling both must do both. Disabling both must do neither (though a span is still created).


*   Interface details: Type: Struct Field
Name: Logger
Location: auth/httptransport/httptransport.go (field on the Options struct)
Signature: Logger *slog.Logger (field on the Options struct)
Description: Accepts a structured logger at client construction time. When NewClient is called with this field set and the experimental logging feature is enabled, the logger is wired into the transport so that error log records are emitted through it. Tests pass a logger via Options when constructing a full client with NewClient.

Type: Struct Field
Name: logger
Location: auth/httptransport/httptransport_otel.go
Signature: logger *slog.Logger (field on the otelAttributeTransport struct)
Description: Holds the structured logger used to emit debug-level log records when actionable errors occur during an HTTP round trip. Tests construct otelAttributeTransport directly with this field set.

Type: Type
Name: errorTrackingBody
Location: auth/httptransport/ (same package as otelAttributeTransport)
Signature: Must implement io.ReadCloser; must be a concrete named type so that callers can perform a type assertion (*errorTrackingBody).
Description: A response-body wrapper that buffers up to 8 KB of the body to extract error message details and defers emitting the structured log record until the body is closed or fully read. When a response has ContentLength greater than 8192, the transport must NOT wrap the body in errorTrackingBody; instead it logs immediately and leaves the body unwrapped.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.