Update the postgres-operator to support user-defined log exporters in OpenTelemetry collector configurations. Ensure that when an instrumentation spec is provided, custom exporters are included in the generated configuration, and log pipelines route through them. Maintain existing behavior when no instrumentation spec is configured.

*   Implement the `NewConfig` function in `internal/collector/config.go`:
    *   Accept a parameter of type `*v1beta1.InstrumentationSpec`.
    *   When `nil`, include only the debug exporter with empty extensions, processors, receivers, and pipelines.
    *   When non-nil, include the debug exporter, all exporters from `spec.Config.Exporters`, and pre-populate processors with `batch/1s`, `batch/200ms`, and `groupbyattrs/compact`.

*   Update `EnablePgAdminLogging` in `internal/collector/pgadmin.go`:
    *   Accept `InstrumentationSpec` as the second parameter.
    *   Use the debug exporter when `spec` is `nil`.
    *   Use exporters from `spec.Logs.Exporters` and add `file_storage/gunicorn` and `file_storage/pgadmin` extensions when non-nil.
    *   Include log transform statements: `set(attributes["log.record.original"], body)`, `set(body, cache["message"])`, and `set(instrumentation_scope.name, cache["name"])`.

*   Implement `NewConfigForPgBackrestRepoHostPod` in `internal/collector/pgbackrest.go`:
    *   Accept `InstrumentationSpec` as the second parameter.
    *   Use the debug exporter when `spec` is `nil`.
    *   Use exporters from `spec.Logs.Exporters` and add `file_storage/pgbackrest_logs` extension and `resource/pgbackrest` processor when non-nil.

*   Update `EnablePatroniLogging` in `internal/collector/patroni.go`:
    *   Always include `resource/patroni` processor before `transform/patroni_logs` and `groupbyattrs/compact` after `batch/200ms`.
    *   Use spec-defined exporters and add `file_storage/patroni_logs` extension when initialized with a non-nil `InstrumentationSpec`.

*   Update `EnablePgBouncerLogging` in `internal/collector/pgbouncer.go`:
    *   Use spec-defined exporters and add `file_storage/pgbouncer_logs` extension and `resource/pgbouncer` processor when initialized with a non-nil `InstrumentationSpec`.

*   Update `EnablePostgresLogging` in `internal/collector/postgres.go`:
    *   Use spec-defined exporters and add `file_storage/pgbackrest_logs` and `file_storage/postgres_logs` extensions, plus `resource/pgbackrest` and `resource/postgres` processors when initialized with a non-nil `InstrumentationSpec`.

*   Ensure all pipeline configurations route logs through exporters named in `spec.Logs.Exporters` when a non-nil `InstrumentationSpec` is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.