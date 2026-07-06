Implement a new feature flag in the integration test package's manifest to enable async process I/O capabilities. Update the helper binary to use async I/O consistent with the test infrastructure.

*   Add a Cargo feature named 'rt-process-io-util' in `tests-integration/Cargo.toml`.
    *   This feature must activate the following tokio sub-features: `tokio/rt`, `tokio/macros`, `tokio/process`, `tokio/io-util`, and `tokio/io-std`.
*   Ensure that when compiled with both the 'full' and 'rt-process-io-util' features, the following integration tests compile and pass:
    *   `try_wait`
    *   `status_closes_any_pipes`
    *   `wait_with_output_captures`
    *   `pipe_from_one_command_to_another`
    *   `feed_a_lot`
*   Update the `test-cat` helper binary located at `tests-integration/src/bin/test-cat.rs`:
    *   Rewrite it as an async program using `#[tokio::main(flavor = "current_thread")]`.
    *   Asynchronously copy all of stdin to stdout using `tokio::io::copy`.
    *   Use `tokio::io::AsyncWriteExt` to flush stdout asynchronously.
*   Modify the `test-cat` binary entry in `tests-integration/Cargo.toml`:
    *   Add `required-features = ["rt-process-io-util"]` to ensure it is only compiled when the new feature is enabled.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.