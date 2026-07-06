I've been looking at the plugin-related API responses and noticed that the authentication policy and installation policy fields are currently typed as optional — they can be null.

*   The `auth_policy` field of `PluginInstallResponse` must be changed from `Option<PluginAuthPolicy>` to `PluginAuthPolicy`, making it always present and required in the plugin install response.

*   The `install_policy` field of `PluginSummary` must be changed from `Option<PluginInstallPolicy>` to `PluginInstallPolicy`, making it always present and required in plugin list responses.

*   The `auth_policy` field of `PluginSummary` must be changed from `Option<PluginAuthPolicy>` to `PluginAuthPolicy`, making it always present and required in plugin list responses.

*   When constructing `PluginSummary` items in the message processor, the `install_policy` and `auth_policy` fields must be populated using direct conversion (not via Option mapping) from the internal plugin data.

*   When constructing `PluginInstallResponse` in the message processor, the `auth_policy` field must be populated using direct conversion (not via Option mapping) from the internal install result.

*   Plugin list responses must include concrete (non-null) `install_policy` and `auth_policy` values on every plugin summary entry, including enabled, disabled, and uninstalled plugins.

*   Plugin install responses must include a concrete (non-null) `auth_policy` value, for example `PluginAuthPolicy::OnInstall` or `PluginAuthPolicy::OnUse`, derived from the marketplace entry rather than from a per-plugin config override.


*   Interface details: Type: Struct
Name: PluginSummary
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: Represents a summary of a plugin returned by the plugin list API. The fields `install_policy` and `auth_policy` must be non-optional (plain values, not wrapped in Option).
Signature:
  pub install_policy: PluginInstallPolicy   // was Option<PluginInstallPolicy>
  pub auth_policy: PluginAuthPolicy         // was Option<PluginAuthPolicy>

Type: Struct
Name: PluginInstallResponse
Location: codex-rs/app-server-protocol/src/protocol/v2.rs
Description: Represents the response from a plugin install operation. The field `auth_policy` must be non-optional (plain value, not wrapped in Option).
Signature:
  pub auth_policy: PluginAuthPolicy         // was Option<PluginAuthPolicy>
  pub apps_needing_auth: Vec<AppSummary>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.