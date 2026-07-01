Implement the migration of the storage extension from the "experimental" namespace to a stable "x" namespace in the OpenTelemetry Collector. Update the storage operation representation to use a plain exported struct and adjust module dependencies and configuration files accordingly.

*   Create a new Go module at `go.opentelemetry.io/collector/extension/xextension`:
    *   Place the storage sub-package at `extension/xextension/storage`.
    *   Ensure this module replaces the old `go.opentelemetry.io/collector/extension/experimental/storage`.

*   Update the storage client interface:
    *   In `extension/xextension/storage/storage.go`, declare the `Batch` method in the `Client` interface with the signature: `Batch(ctx context.Context, ops ...*Operation) error`.
    *   Ensure `Batch` accepts operations as variadic pointers to the `Operation` struct.

*   Define the `Operation` struct:
    *   Export the `Operation` struct with fields `Key string`, `Value []byte`, and `Type OpType`.
    *   Export the operation kind type as `OpType` with constants `Get`, `Set`, and `Delete` defined using `iota`.

*   Implement factory functions:
    *   `GetOperation(key string) *Operation` returns a pointer to an `Operation` of type `Get`.
    *   `SetOperation(key string, value []byte) *Operation` returns a pointer to an `Operation` of type `Set`.
    *   `DeleteOperation(key string) *Operation` returns a pointer to an `Operation` of type `Delete`.

*   Update module dependencies:
    *   In `exporter/exportertest/go.mod`, list `go.opentelemetry.io/collector/extension/xextension` as an indirect dependency and include the replace directive: `replace go.opentelemetry.io/collector/extension/xextension => ../../extension/xextension`.
    *   Remove the old `extension/experimental/storage` dependency and its replace directive.

*   Modify other configuration files:
    *   In `otelcol/otelcoltest/go.mod`, remove the replace directive for `extension/experimental/storage` and add: `replace go.opentelemetry.io/collector/extension/xextension => ../../extension/xextension`.
    *   In `cmd/builder/test/core.builder.yaml`, replace the entry for `extension/experimental/storage` with: `- go.opentelemetry.io/collector/extension/xextension => ${WORKSPACE_DIR}/extension/xextension`.
    *   In `cmd/builder/internal/builder/main_test.go`, ensure the `replaceModules` slice contains `/extension/xextension` and does not contain `/extension/experimental/storage`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.