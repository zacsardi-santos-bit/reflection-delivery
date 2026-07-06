Implement methods to efficiently identify YAML document formats for service-level objectives by checking specific header fields. Update each spec loader to expose a method that determines if a YAML document matches its format before full parsing.

*   Implement the `IsSpecType` method for `YAMLSpecLoader` in `internal/k8sprometheus/spec.go`.
    *   Return `true` if the YAML contains both `apiVersion: sloth.slok.dev/v1` and `kind: PrometheusServiceLevel`.
    *   Ensure detection works with values that are unquoted, single-quoted, double-quoted, or have extra whitespace.
    *   Return `false` for empty input, malformed YAML, incorrect `apiVersion` (e.g., `sloth.slok.dev/v2`), or incorrect `kind` (e.g., `PrometheusService`).

*   Implement the `IsSpecType` method for `yamlSpecLoader` in `internal/openslo/spec.go`, accessed via `openslo.YAMLSpecLoader`.
    *   Return `true` if the YAML contains both `apiVersion: openslo/v1alpha` and `kind: SLO`.
    *   Ensure detection works with values that are unquoted, single-quoted, double-quoted, or have extra whitespace.
    *   Return `false` for empty input, malformed YAML, incorrect `apiVersion` (e.g., `openslo/v1`), or incorrect `kind` (e.g., `service`).

*   Implement the `IsSpecType` method for `YAMLSpecLoader` in `internal/prometheus/spec.go`.
    *   Return `true` if the YAML contains `version: prometheus/v1`.
    *   Ensure detection works with values that are unquoted, single-quoted, double-quoted, or have extra whitespace.
    *   Return `false` for empty input, malformed YAML, or incorrect `version` (e.g., `prometheus/v2`).

*   Ensure all `IsSpecType` methods accept a `context.Context` and a `byte` slice as parameters and return a `bool`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.