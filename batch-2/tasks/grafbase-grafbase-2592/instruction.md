Implement a single-step asynchronous test runner initialization for the Grafbase extension testing framework. Ensure that the test runner starts all necessary servers automatically upon creation and update the CLI-generated scaffolding to reflect these changes.

*   Update `TestRunner::new()` in `crates/grafbase-sdk/src/test/runner.rs`:
    *   Change the function to be asynchronous: `pub async fn new(config: TestConfig) -> anyhow::Result<Self>`.
    *   Internally start all gateway and mock subgraph servers before returning the initialized runner.
    *   Call `DynamicSubgraph::start().await` for each subgraph and use the resulting `MockGraphQlServer` to retrieve `sdl()`, `name()`, and `url()` values.
    *   Ensure `MockGraphQlServer` and `DynamicSubgraph` are imported from the `grafbase_sdk_mock` crate.

*   Modify `start_servers()` in `crates/grafbase-sdk/src/test/runner.rs`:
    *   Make the method private (remove `pub` visibility).
    *   Ensure it is called internally by `TestRunner::new()`.

*   Update CLI-generated extension scaffolding:
    *   In `cli/templates/extension/tests/integration_tests.rs.template`, use the async constructor pattern: `let runner = TestRunner::new(config).await.unwrap()`.
        *   Do not use `mut` binding for the runner variable.
        *   Remove any separate server start call and related comments.
    *   In `cli/tests/extension/mod.rs`, ensure the Cargo.toml specifies `grafbase-sdk` version "0.1.9" for both `[dependencies]` and `[dev-dependencies]`.
    *   Use `//` for comments in the generated integration test template instead of `///`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.