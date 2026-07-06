Implement a feature in the vllm Rust server to attach a request tracking identifier to HTTP responses, similar to the existing functionality in the Python server. Ensure this feature is opt-in and configurable via command-line and JSON configuration.

*   Update the `FrontendConfig` struct in `rust/src/server/src/config.rs`:
    *   Add a `pub enable_request_id_headers: bool` field.
    *   Default this field to `false`.

*   Modify the `SharedRuntimeArgs` struct in `rust/src/cmd/src/cli.rs`:
    *   Add a `pub enable_request_id_headers: bool` field.
    *   Annotate with `#[arg(long)]` for the `--enable-request-id-headers` CLI flag.
    *   Annotate with `#[serde(default)]` for JSON deserialization.
    *   Ensure `to_frontend_config()` sets `enable_request_id_headers` to `true` when `--enable-request-id-headers` is used.

*   Adjust the `AppState` struct in `rust/src/server/src/state.rs`:
    *   Add a `pub enable_request_id_headers: bool` field.
    *   Default this field to `false` in `AppState::new()`.
    *   Implement a `with_request_id_headers(self, enabled: bool) -> Self` method to set this field.

*   Implement HTTP response behavior based on `enable_request_id_headers`:
    *   If `enable_request_id_headers` is `false`, do not include an `x-request-id` header.
    *   If `enable_request_id_headers` is `true`:
        *   If the incoming request lacks an `X-Request-Id` header, generate a 32 ASCII hex digit UUID v4 and include it as `x-request-id`.
        *   If the incoming request includes an `X-Request-Id` header, echo this value in the `x-request-id` response header.

*   Ensure the `serve` subcommand:
    *   Recognizes the `--enable-request-id-headers` flag.
    *   Produces a `FrontendConfig` with `enable_request_id_headers = true` when the flag is used.

*   Ensure the `frontend` subcommand:
    *   Recognizes the `--args-json` with `"enable_request_id_headers":true`.
    *   Produces a `FrontendConfig` with `enable_request_id_headers = true` when specified.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.