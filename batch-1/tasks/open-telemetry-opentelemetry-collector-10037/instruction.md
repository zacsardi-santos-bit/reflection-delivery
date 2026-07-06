Split the metadata code generator's output into two distinct files: one for component type/stability information and another for telemetry provider helpers. Ensure that telemetry helpers use the component's module path as the instrumentation scope name. Update the generator to include new templates for telemetry implementation and tests, and regenerate files for all existing components.

*   Modify the code generator to produce two separate files:
    *   `generated_telemetry.go` using `telemetry.go.tmpl` for telemetry provider helpers.
    *   `generated_status.go` using `status.go.tmpl` for component type/stability information.
*   Update `status.go.tmpl`:
    *   Remove imports for `go.opentelemetry.io/otel/metric` and `go.opentelemetry.io/otel/trace`.
    *   Exclude Meter and Tracer function definitions.
    *   Retain only the component package import, Type variable, and stability-level constants.
*   Create `telemetry.go.tmpl`:
    *   Include imports for `go.opentelemetry.io/collector/component`, `go.opentelemetry.io/otel/metric`, and `go.opentelemetry.io/otel/trace`.
    *   Define `Meter` and `Tracer` functions using the component's scope name.
    *   Use an empty string for scope name if not specified.
*   Define the `Meter` function:
    *   Signature: `Meter(settings component.TelemetrySettings) metric.Meter`
    *   Return a meter from `settings.MeterProvider.Meter` with the component's scope name.
*   Define the `Tracer` function:
    *   Signature: `Tracer(settings component.TelemetrySettings) trace.Tracer`
    *   Return a tracer from `settings.TracerProvider.Tracer` with the component's scope name.
*   Embed new templates in the generator:
    *   `telemetry.go.tmpl` and `telemetry_test.go.tmpl` in `templates/` directory.
*   Regenerate files for each component's `internal/metadata` package:
    *   Define `Meter` and `Tracer` with the component's Go module import path as the scope name.
    *   Ensure scope names match specified paths for each component.
*   Update `main.go`:
    *   Use `generateFile` with `telemetry.go.tmpl` to create `generated_telemetry.go`.
    *   Use `generateFile` with `telemetry_test.go.tmpl` to create `generated_telemetry_test.go` for components with a status section.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.