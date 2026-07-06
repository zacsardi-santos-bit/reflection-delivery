Implement the forwarding of the shutdown timeout and log stats disable flag from the vllm Rust CLI's `serve` command to the Python engine subprocess. Ensure these configurations are respected by the engine to allow for graceful shutdowns and optional suppression of statistics logging.

*   Update the `RuntimeArgs` struct in `rust/src/cmd/src/cli.rs`:
    *   Add a `shutdown_timeout: u64` field to accept the `--shutdown-timeout <value>` CLI argument.
    *   Add a `disable_log_stats: bool` field to accept the `--disable-log-stats` CLI flag, defaulting to false.

*   Modify the `ServeArgs::to_managed_engine_config` method in `rust/src/cmd/src/cli.rs`:
    *   Ensure it includes `"--shutdown-timeout"` followed by the numeric value in `ManagedEngineConfig.python_args` when `shutdown_timeout` is greater than 0.
    *   Ensure it includes `"--disable-log-stats"` in `ManagedEngineConfig.python_args` when `disable_log_stats` is true.
    *   Pass `self.runtime.disable_log_stats` and `self.runtime.shutdown_timeout` to the managed-engine config builder.

*   Update the `to_config` function in `rust/src/managed-engine/src/cli.rs`:
    *   Accept `disable_log_stats: bool` and `shutdown_timeout: u64` as parameters.
    *   Append `"--disable-log-stats"` to `python_args` if `disable_log_stats` is true.
    *   Append `"--shutdown-timeout"` and its string value to `python_args` if `shutdown_timeout` is greater than 0.

*   Ensure the call site in `rust/src/cmd/src/cli.rs` where `ServeArgs::to_managed_engine_config` is invoked passes the appropriate arguments for `disable_log_stats` and `shutdown_timeout`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.