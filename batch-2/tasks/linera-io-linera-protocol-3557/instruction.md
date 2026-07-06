Implement required configuration fields for profiling service integration in both shard and validator-level configurations. Ensure these fields are mandatory to prevent silent misconfigurations and update related functions and tests accordingly.

*   Update the `ShardConfig` struct in `linera-rpc/src/config.rs`:
    *   Add a required field `pyroscope_host` of type `String`.
    *   Add an optional field `pyroscope_port` of type `Option<u16>`.
    *   Ensure deserialization fails with an error if `pyroscope_host` is omitted.

*   Update the `ValidatorOptions` struct in `linera-service/src/server.rs`:
    *   Add a required field `pyroscope_host` of type `String`.
    *   Add a required field `pyroscope_port` of type `u16`.
    *   Ensure deserialization fails with an error if `pyroscope_host` is omitted.

*   Modify the `generate_shard_configs` function in `linera-service/src/server.rs`:
    *   Extend the function signature to include `pyroscope_host` of type `String` and `pyroscope_port` of type `Option<String>`.
    *   Apply pattern substitution to `pyroscope_host` and parse `pyroscope_port` as `u16` after substitution.
    *   Populate each `ShardConfig` with the substituted `pyroscope_host` and parsed `pyroscope_port`.

*   Update all existing configuration files and test fixtures:
    *   Include valid values for the new `pyroscope_host` and `pyroscope_port` fields in `ShardConfig` and `ValidatorOptions` to ensure successful deserialization.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.