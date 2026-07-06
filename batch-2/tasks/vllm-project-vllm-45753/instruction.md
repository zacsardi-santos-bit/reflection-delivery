Implement CORS support in the Rust frontend of the vLLM serving system to allow browser-based clients from different origins to access the API. Add command-line flags and JSON configuration options to specify allowed origins, HTTP methods, headers, and credential forwarding. Ensure the defaults are permissive, matching the Python server behavior.

*   Update the `SharedRuntimeArgs` struct in `rust/src/cmd/src/cli.rs`:
    *   Add fields: `allowed_origins`, `allowed_methods`, `allowed_headers` (all of type `JsonStringList`, default `JsonStringList(["*"])`), and `allow_credentials` (boolean, default `false`).
    *   Ensure CLI accepts `--allowed-origins`, `--allowed-methods`, `--allowed-headers` (JSON array strings), and `--allow-credentials` (boolean).
    *   Modify unsupported argument tests to use `--ssl-keyfile` with the error message: `invalid value '/tmp/key.pem' for '--ssl-keyfile <SSL_KEYFILE>': argument is not implemented in Rust frontend yet`.

*   Modify JSON configuration handling:
    *   Ensure `--args-json` accepts `allowed_origins` and `allow_credentials` fields.
    *   Default unspecified list fields to `["*"]` using serde defaults.
    *   Update unsupported field tests to reference `ssl_keyfile` and `response_role`.

*   Update server configuration in `rust/src/server/src/config.rs`:
    *   Add a `cors: CorsConfig` field to the `Config` struct.
    *   Create a `CorsConfig` struct with fields: `allow_origins`, `allow_methods`, `allow_headers` (all `Vec<String>`, default `["*"]`), and `allow_credentials` (boolean, default `false`).
    *   Ensure `CorsConfig` derives `Debug`, `Clone`, `PartialEq`, `Eq`, `Serialize`, and implements `Default`.
    *   Re-export `CorsConfig` from the crate's public API.

*   Implement CORS middleware handling:
    *   Add a `with_cors` method to `AppState` in `rust/src/server/src/state.rs` to store `CorsConfig`.
    *   Ensure default CORS config (`*` origins, no credentials) returns `access-control-allow-origin: *` without `Vary` header.
    *   Ensure OPTIONS preflight requests return `access-control-allow-methods: DELETE,GET,HEAD,OPTIONS,PATCH,POST,PUT` and mirror requested headers in `access-control-allow-headers`.
    *   Ensure requests without `Origin` header receive no CORS headers.
    *   Handle explicit origin allowlist by reflecting matched origin and adding `Vary: origin`.
    *   Reflect request origin for wildcard origins with `allow_credentials: true` and include `access-control-allow-credentials: true`.
    *   Ensure unauthorized (401) responses omit CORS headers.
    *   Bypass API key authentication for OPTIONS preflight requests.
    *   Expand wildcard method list to explicit HTTP verbs in preflight responses.
    *   Union explicit headers with CORS safelist headers, returning them in a lowercased, sorted format.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.