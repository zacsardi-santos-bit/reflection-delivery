Implement a gRPC-based log exporter for the OpenTelemetry C++ SDK to export log records to an OpenTelemetry Collector. Ensure it integrates with the existing log provider and batch processor infrastructure, and supports full log record context and configuration options.

*   Implement the `OtlpGrpcLogExporter` class in `exporters/otlp/include/opentelemetry/exporters/otlp/otlp_grpc_log_exporter.h`.
    *   Extend `opentelemetry::sdk::logs::LogExporter`.
    *   Provide constructors:
        *   `OtlpGrpcLogExporter()`
        *   `OtlpGrpcLogExporter(const OtlpGrpcExporterOptions &options)`
        *   `OtlpGrpcLogExporter(std::unique_ptr<proto::collector::logs::v1::LogsService::StubInterface> stub)`
    *   Implement `MakeRecordable()` to return a `std::unique_ptr<opentelemetry::sdk::logs::Recordable>`.
    *   Implement `Export()` to accept a `nostd::span` of `unique_ptr<sdk::logs::Recordable>` and return `sdk::common::ExportResult::kSuccess` or `sdk::common::ExportResult::kFailure` based on gRPC call status.
    *   Implement `Shutdown()` to ensure subsequent `Export()` calls return `sdk::common::ExportResult::kFailure`.
    *   Include private members:
        *   `const OtlpGrpcExporterOptions options_`
        *   `std::unique_ptr<proto::collector::logs::v1::LogsService::StubInterface> log_service_stub_`
    *   Declare `OtlpGrpcLogExporterTestPeer` as a friend class.

*   Ensure log records support:
    *   Trace context fields: trace ID, span ID, trace flags.
    *   Severity, name, body/message.
    *   Key-value attributes of types: bool, int32, uint32, int64, uint64, double, string, and arrays of these types.

*   Define `OtlpGrpcExporterOptions` struct in `exporters/otlp/include/opentelemetry/exporters/otlp/otlp_grpc_exporter_options.h`.
    *   Include fields: `endpoint`, `use_ssl_credentials`, `ssl_credentials_cacert_path`, `ssl_credentials_cacert_as_string`, `timeout`, `metadata`.

*   Implement the source file for `OtlpGrpcLogExporter` in `exporters/otlp/src/otlp_grpc_log_exporter.cc`.
    *   Wrap the implementation with `ENABLE_LOGS_PREVIEW` preprocessor guard.

*   Update the Bazel BUILD file:
    *   Add a `cc_library` target named `otlp_grpc_log_exporter`.
    *   Add a `cc_test` target named `otlp_grpc_log_exporter_test`.

*   Integrate the exporter with `BatchLogProcessor` and `LoggerProvider` to ensure logs are processed and exported correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.