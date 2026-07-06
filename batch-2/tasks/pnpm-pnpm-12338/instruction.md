I'm working with pnpm in a CI environment where registry authentication tokens are injected as environment variables rather than stored in configuration files.

*   When environment variables with the case-insensitive prefix 'npm_config_//' are present, the part after the prefix (starting with '//') must be added to the returned configuration's auth settings with the env var's value.

*   When environment variables with the case-insensitive prefix 'pnpm_config_//' are present, the part after the prefix (starting with '//') must similarly be added to the auth settings.

*   When both an 'npm_config_//' and a 'pnpm_config_//' environment variable supply the same URL-scoped key, the 'pnpm_config_//' value must take precedence and override the 'npm_config_//' value.

*   The prefix matching for 'npm_config_//' and 'pnpm_config_//' must be case-insensitive, so variables like 'NPM_CONFIG_//...' and 'PnPm_Config_//...' are recognized equally.

*   The 'tokenHelper' credential field must be silently ignored when supplied via a URL-scoped environment variable; it must not appear in the auth settings and must not trigger any error.

*   URL-scoped authentication from environment variables must override the same key present in a project .npmrc file for the same host; the environment value wins.

*   CLI-provided URL-scoped authentication tokens must override values for the same key from environment variables; precedence is CLI > environment > project config file.

*   Non-token credential fields (such as username and password) must be supported via URL-scoped environment variables, just like auth tokens.

*   Environment variables that do not contain '//' after the config prefix (i.e. non-URL-scoped keys) must not be imported into the auth settings.

*   The NpmrcAuth::from_url_scoped_env associated function must enumerate all environment variables via the EnvVar trait's vars() method, identify entries with 'npm_config_//' or 'pnpm_config_//' prefixes (case-insensitive), and populate creds_by_uri accordingly.

*   Environment variables with empty string values must be treated as unset and must not be imported into creds_by_uri.

*   The prefix check in the URL-scoped env var parser must be byte-safe: environment variable names containing multi-byte (non-ASCII) characters must not cause a panic.


*   Interface details: Type: Function
Name: from_url_scoped_env
Location: pacquet/crates/config/src/npmrc_auth.rs (as an associated function on NpmrcAuth)
Signature: pub fn from_url_scoped_env<Sys: EnvVar>() -> NpmrcAuth
Description: Associated function on NpmrcAuth that enumerates all environment variables via Sys::vars(), identifies those whose names case-insensitively match the prefixes "npm_config_//" or "pnpm_config_//", strips the prefix to extract the URL-scoped key, and populates creds_by_uri with the parsed credentials. When both prefixes supply the same URL-scoped key, the pnpm_config_// value wins. Empty string values are treated as unset and skipped. Non-ASCII env var names must not cause a panic during the byte-index prefix check.

Type: Trait Method
Name: vars
Location: pacquet/crates/env-replace/src/lib.rs (on the existing EnvVar trait)
Signature: fn vars() -> Vec<(String, String)>
Description: New method on the EnvVar trait that returns all current environment variable entries as a list of (name, value) string pairs. It has a default implementation returning an empty Vec so existing EnvVar implementors do not need to be updated. Production providers (Host, SystemEnv) and test helpers (FakeEnv) must override it with real enumeration. Required by NpmrcAuth::from_url_scoped_env to enumerate and prefix-match environment variables.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.