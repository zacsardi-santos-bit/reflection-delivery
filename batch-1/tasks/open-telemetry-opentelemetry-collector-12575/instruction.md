Update the codebase to reflect the relocation of the OpenTelemetry contrib library to a new module path. Ensure all references, dependencies, and checksums are correctly updated to maintain functionality and pass all existing tests.

*   Update all Go source files:
    *   Change import paths from `go.opentelemetry.io/contrib/config/v0.3.0` to `go.opentelemetry.io/contrib/otelconf/v0.3.0`.
    *   Retain the local alias `config` for the import.

*   Modify the `go.mod` file:
    *   Replace the `go.opentelemetry.io/contrib/config` dependency with `go.opentelemetry.io/contrib/otelconf`.
    *   Update OpenTelemetry package versions:
        *   Core OTel packages from v1.34.x to v1.35.x.
        *   Log/metric sub-packages from v0.10.x to v0.11.x.

*   Update `go.sum` files:
    *   Include checksums for `go.opentelemetry.io/contrib/otelconf` and all updated dependency versions.
    *   Remove checksums for the deprecated `go.opentelemetry.io/contrib/config` module.

*   Ensure all telemetry-related tests:
    *   Tests for attributes, service configuration, telemetry initialization, logging, metrics, and tracing must pass without changes to their behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.