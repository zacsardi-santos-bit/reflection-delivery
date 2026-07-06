Enhance the session middleware configuration in your Rust web framework to support additional session cookie settings through the TOML-based project configuration file. Implement the ability to configure the same-site policy, HTTP-only flag, cookie domain, path, name, always save option, and expiry settings.

*   Define a `SameSite` enum in `cot/src/config.rs`:
    *   Variants: `Strict` (default), `Lax`, `None`
    *   Deserializable from lowercase strings ("strict", "lax", "none") in TOML
    *   Implement `PartialEq`, `Copy`, `Clone`
    *   Implement `From<SameSite>` for `tower_sessions::cookie::SameSite`

*   Update `SessionMiddlewareConfig` in `cot/src/config.rs`:
    *   Add a `same_site: SameSite` field with default `SameSite::Strict`
    *   Add fields with specified defaults:
        *   `http_only: bool` (default `true`)
        *   `domain: Option<String>` (default `None`)
        *   `path: String` (default `"/"`)
        *   `name: String` (default `"id"`)
        *   `always_save: bool` (default `false`)
    *   Ensure these fields are parseable from `[middlewares.session]` in TOML

*   Define an `Expiry` enum in `cot/src/config.rs`:
    *   Variants: `OnSessionEnd` (default), `OnInactivity(std::time::Duration)`, `AtDateTime(chrono::DateTime<chrono::FixedOffset>)`
    *   Implement `PartialEq`, `Copy`, `Clone`
    *   Implement `From<Expiry>` for `tower_sessions::Expiry`

*   Add an `expiry: Expiry` field to `SessionMiddlewareConfig`:
    *   Default to `Expiry::OnSessionEnd`
    *   Use `#[serde(with = "crate::serializers::session_expiry_time")]`
    *   Deserialize humantime duration strings to `Expiry::OnInactivity`
    *   Deserialize RFC 3339 timestamp strings to `Expiry::AtDateTime`
    *   Return an error for invalid expiry strings during `ProjectConfig::from_toml()`

*   Implement a custom serde module `session_expiry_time` in `cot/src/serializers.rs`:
    *   Serialize `Expiry::OnSessionEnd` as `null`
    *   Serialize `Expiry::OnInactivity(duration)` as a humantime string
    *   Serialize `Expiry::AtDateTime(dt)` as a string representation of the datetime
    *   Deserialize strings with humantime::parse_duration or DateTime::parse_from_rfc3339
    *   Deserialize `None/null` to `Expiry::OnSessionEnd`
    *   Produce a deserialization error for invalid strings

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.