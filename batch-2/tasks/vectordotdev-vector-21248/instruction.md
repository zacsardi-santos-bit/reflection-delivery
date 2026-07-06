Implement a new PostgreSQL sink component for Vector to write batches of events into a specified PostgreSQL database table. Ensure the sink is configurable via a TOML file, supports connection pooling, and integrates with Vector's existing architecture.

*   Create a `PostgresConfig` struct in `src/sinks/postgres/config.rs`:
    *   Include public fields `endpoint: String` and `table: String`.
    *   Implement `GenerateConfig` with a `generate_config()` method returning a `toml::Value` containing `endpoint = "postgres://user:password@localhost/default"` and `table = "table"`.
    *   Ensure it derives `Clone`, `Default`, `Debug`, and uses `#[serde(deny_unknown_fields)]`.
    *   Implement `SinkConfig` with `#[typetag::serde(name = "postgres")]`.

*   Update `Cargo.toml`:
    *   Define a `sinks-postgres` feature flag with `sqlx` as an optional dependency (version 0.8.3, features: derive, postgres, chrono, runtime-tokio, default-features = false).
    *   Add `sinks-postgres` to the `sinks-logs` feature group.
    *   Declare `postgres_sink-integration-tests = ["sinks-postgres"]`.

*   Modify `src/sinks/mod.rs`:
    *   Register the postgres module with `#[cfg(feature = "sinks-postgres")] pub mod postgres;`.

*   Develop the `src/sinks/postgres/mod.rs` module:
    *   Declare submodules `config`, `service`, and `sink`.
    *   Re-export `PostgresConfig` with `pub use self::config::PostgresConfig`.

*   Implement `PostgresService` in `src/sinks/postgres/service.rs`:
    *   Create a constructor `new(connection_pool: Pool<Postgres>, table: String, endpoint: String) -> Self`.
    *   Implement `PostgresRetryLogic` as `Clone` and `RetryLogic` for PostgreSQL errors.
    *   Define `PostgresRequest` implementing `TryFrom<Vec<Event>>`, `Finalizable`, and `MetaDescriptive`.
    *   Define `PostgresResponse` implementing `DriverResponse`.

*   Implement `PostgresSink` in `src/sinks/postgres/sink.rs`:
    *   Create a constructor `new(service: Svc<PostgresService, PostgresRetryLogic>, batch_settings: BatcherSettings) -> Self`.
    *   Implement `StreamSink<Event>`.

*   Ensure the entire `sinks::postgres` module compiles without errors under the `sinks-postgres` feature.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.