I'm working on the ProxmoxVE integration for Home Assistant and I need to add support for API token authentication alongside the existing password-based authentication.

*   Must define the following constants in homeassistant/components/proxmoxve/const.py: CONF_AUTH_METHOD with string value 'auth_method'; CONF_TOKEN with string value 'token'; CONF_TOKEN_ID with string value 'token_id'; CONF_TOKEN_SECRET with string value 'token_value' (not 'token_secret'); AUTH_PAM with string value 'pam'.

*   CONF_AUTH_METHOD must be importable from both homeassistant.components.proxmoxve.const and directly from homeassistant.components.proxmoxve (re-exported from the package __init__.py).

*   The config flow must split into two sequential steps: a 'user' step (step_id='user') that collects the authentication method, host, username, port, a boolean token-usage flag, and SSL verification settings; followed by a 'user_auth' step (step_id='user_auth') that collects authentication credentials.

*   The 'user_auth' step credential schema must vary based on the values collected in the 'user' step: when auth method is not 'other' and token flag is False, collect only CONF_PASSWORD; when auth method is not 'other' and token flag is True, collect CONF_TOKEN_ID and CONF_TOKEN_SECRET; when auth method is 'other' and token flag is False, collect CONF_PASSWORD and CONF_REALM; when auth method is 'other' and token flag is True, collect CONF_TOKEN_ID, CONF_TOKEN_SECRET, and CONF_REALM.

*   When validation errors occur during the 'user_auth' step (e.g. authentication failure, connection error, no nodes found), the flow must return to the 'user_auth' step with errors dict {'base': <reason_string>}; the user can resubmit credentials to retry.

*   ProxmoxveConfigFlow.VERSION must be 3.

*   The async_migrate_entry function must handle migration from config entry version 2 to version 3: read the existing CONF_REALM value, set CONF_AUTH_METHOD to AUTH_PAM ('pam') when the lowercased realm equals 'pam', and update the entry version to 3. After migration from version 2, entry.data[CONF_AUTH_METHOD] must equal AUTH_PAM and entry.data[CONF_REALM] must equal AUTH_PAM ('pam') for entries whose realm was 'pam'.

*   Migration from config entry version 1 must result in version 3 (not version 2 as before). The full migration chain from v1 must produce an entry at version 3.

*   The reauth confirm step (step_id='reauth_confirm') for a config entry using token authentication with auth method 'other' must accept a form containing CONF_REALM, CONF_TOKEN_ID, and CONF_TOKEN_SECRET. After successful reauth, CONF_TOKEN_SECRET in the config entry data must be updated to the newly provided secret.

*   Config entry data must include CONF_AUTH_METHOD and CONF_TOKEN fields. These fields must appear in diagnostics output. The config entry version reported in diagnostics must be 3.


*   Interface details: ## Constants

Type: Constant
Name: CONF_AUTH_METHOD
Location: homeassistant/components/proxmoxve/const.py
Value: "auth_method"
Description: Configuration key for the authentication method (e.g. "pam", "pve", "other"). Must also be exported from homeassistant/components/proxmoxve/__init__.py so it is importable from the package directly.

Type: Constant
Name: CONF_TOKEN
Location: homeassistant/components/proxmoxve/const.py
Value: "token"
Description: Configuration key for a boolean flag indicating whether API token authentication is used instead of password authentication.

Type: Constant
Name: CONF_TOKEN_ID
Location: homeassistant/components/proxmoxve/const.py
Value: "token_id"
Description: Configuration key for the API token identifier (the name given to the token in Proxmox).

Type: Constant
Name: CONF_TOKEN_SECRET
Location: homeassistant/components/proxmoxve/const.py
Value: "token_value"
Description: Configuration key for the API token secret. IMPORTANT: the string value is "token_value", NOT "token_secret".

Type: Constant
Name: AUTH_PAM
Location: homeassistant/components/proxmoxve/const.py
Value: "pam"
Description: String constant representing the PAM (Linux user) authentication method. Used both as the auth method name and as the default realm value for PAM authentication.

## Config Flow

Type: Class
Name: ProxmoxveConfigFlow
Location: homeassistant/components/proxmoxve/config_flow.py
Description: Config flow for Proxmox VE. VERSION must be 3. The flow is split into two steps: "user" (base connection + auth type selection) and "user_auth" (credentials).

Signature: async_step_user(user_input: dict[str, Any] | None = None) -> ConfigFlowResult
Description: Step with step_id="user". Collects CONF_AUTH_METHOD, CONF_HOST, CONF_USERNAME, CONF_PORT, CONF_TOKEN (boolean), CONF_VERIFY_SSL. When user_input is provided, advances to async_step_user_auth.

Signature: async_step_user_auth(user_input: dict[str, Any] | None = None) -> ConfigFlowResult
Description: Step with step_id="user_auth". Collects credentials whose schema depends on the auth method and token flag from the previous step:
- CONF_AUTH_METHOD != "other" and CONF_TOKEN is False: schema collects only CONF_PASSWORD
- CONF_AUTH_METHOD != "other" and CONF_TOKEN is True: schema collects CONF_TOKEN_ID and CONF_TOKEN_SECRET
- CONF_AUTH_METHOD == "other" and CONF_TOKEN is False: schema collects CONF_PASSWORD and CONF_REALM
- CONF_AUTH_METHOD == "other" and CONF_TOKEN is True: schema collects CONF_TOKEN_ID, CONF_TOKEN_SECRET, and CONF_REALM
On success, creates the config entry. On error, returns to this same step with {"base": reason} errors.

## Migration

Type: Function
Name: async_migrate_entry
Location: homeassistant/components/proxmoxve/__init__.py
Signature: async_migrate_entry(hass: HomeAssistant, entry: ProxmoxConfigEntry) -> bool
Description: Handles config entry migration. Must handle v2 → v3 migration in addition to the existing v1 → v2 migration (so a v1 entry ends up at v3 after a full migration pass). For v2 → v3: reads the existing CONF_REALM value, sets CONF_AUTH_METHOD to AUTH_PAM ("pam") when the realm is "pam", and updates the entry to version 3. After migration from either v1 or v2, entry.version must equal 3 and entry.data[CONF_AUTH_METHOD] must equal AUTH_PAM for entries that originally had realm="pam".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.