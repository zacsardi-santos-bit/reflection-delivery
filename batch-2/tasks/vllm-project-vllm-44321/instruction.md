Implement API key authentication for the vllm Rust frontend server to restrict access to inference and model-listing endpoints. Configure API keys via a repeatable CLI flag or JSON arguments path, and enforce bearer token authentication for protected routes.

*   Update the `Config` struct in `rust/src/server/src/config.rs`:
    *   Add a field `pub api_keys: Vec<String>` to hold accepted API keys.
    *   Implement a custom Debug formatter for `api_keys` to display as `[<redacted>; N]` for non-empty lists and `[]` for empty lists.

*   Modify the `serve` subcommand in `rust/src/cmd/src/cli.rs`:
    *   Add a repeatable `--api-key` CLI flag (`#[arg(long)] api_key: Vec<String>`) to collect API keys into `config.api_keys`.

*   Update JSON-based argument handling:
    *   Ensure the `--args-json` frontend argument accepts an `api_key` field as a single string or an array of strings.
    *   Map the `api_key` field to `Config::api_keys` in the resulting configuration.
    *   Exclude `api_key` from unsupported-arguments error messages.

*   Implement the `with_api_keys` method in `rust/src/server/src/state.rs`:
    *   Define `pub fn with_api_keys(self, api_keys: Vec<String>) -> Self` to configure API key authentication for guarded routes.
    *   Ensure requests to `/v1`, `/v2`, or `/inference` require a valid `Authorization: Bearer <key>` header, returning HTTP 401 with `{"error": "Unauthorized"}` for unauthorized access.
    *   Allow OPTIONS requests and routes outside guarded prefixes (e.g., `/health`) without authentication.

*   Ensure API key values do not appear in logs or debug output:
    *   When printing server configuration, replace actual keys with a redacted placeholder showing only the count of keys.

*   Maintain existing configuration snapshot tests to include `api_keys: []` when no keys are configured.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.