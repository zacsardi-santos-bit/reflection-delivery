I'm using the Codex client library and I've noticed that it automatically adds environment context information to user messages before sending them to the API.

*   The `Config` struct in `codex-rs/core/src/config/mod.rs` must gain a boolean field named `include_environment_context` that defaults to `true` to preserve existing behavior

*   The `ConfigToml` struct in the same file must gain an `include_environment_context: Option<bool>` field so the flag can be set in the on-disk config file; the config loading logic must resolve this to a `bool` defaulting to `true` when absent

*   The `ConfigProfile` struct in `codex-rs/core/src/config/profile.rs` must gain an `include_environment_context: Option<bool>` field so profile-level overrides work; profile value takes precedence over top-level config value

*   When `include_environment_context` is `false`, the client must not include any `<environment_context>` tag or its contents in user-role messages sent to the API — this must be observable in the request body's input field


*   Interface details: Type: Struct Field
Name: include_environment_context
Location: codex-rs/core/src/config/mod.rs
Signature: include_environment_context: bool
Description: Boolean field on the `Config` struct that controls whether the client injects environment context into user-role messages sent to the API. Defaults to `true` to preserve existing behavior. When set to `false`, no `<environment_context>` tag or content is included in user-role messages in outgoing API requests.

Type: Struct Field
Name: include_environment_context
Location: codex-rs/core/src/config/mod.rs
Signature: include_environment_context: Option<bool>
Description: Optional boolean field on the `ConfigToml` struct (the on-disk config representation). When absent, the loaded `Config` defaults this to `true`. When present, its value overrides the default. Profile-level settings take precedence over the top-level config file setting.

Type: Struct Field
Name: include_environment_context
Location: codex-rs/core/src/config/profile.rs
Signature: include_environment_context: Option<bool>
Description: Optional boolean field on the `ConfigProfile` struct. When present, its value takes precedence over the `ConfigToml`-level setting when loading the active profile.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.