I'm working on the ecobee integration for Home Assistant and I'd like to add support for username and password authentication as an alternative to the existing API key and PIN-based setup flow.

*   When only CONF_USERNAME and CONF_PASSWORD are provided in the user step (no CONF_API_KEY), the config flow must attempt credential-based authentication by instantiating Ecobee with config={ECOBEE_USERNAME: username, ECOBEE_PASSWORD: password} and calling refresh_tokens().

*   If refresh_tokens() returns True during credential authentication, the flow must create a config entry with title equal to DOMAIN and data containing CONF_USERNAME, CONF_PASSWORD, and CONF_REFRESH_TOKEN (the refresh_token value from the Ecobee instance). CONF_API_KEY must NOT be included in this data.

*   If refresh_tokens() returns False and only CONF_USERNAME and CONF_PASSWORD were provided (no CONF_API_KEY), the flow must return the user form with errors={'base': 'login_failed'}.

*   If CONF_API_KEY was provided alongside CONF_USERNAME and CONF_PASSWORD in the user step, the flow must return the user form with errors={'base': 'invalid_auth'}, regardless of any authentication attempt.

*   After a credential authentication failure (either 'login_failed' or 'invalid_auth'), the user must remain on the user form (step_id='user') and be able to re-submit with valid credentials to successfully create a config entry.

*   The strings.json file for the ecobee component must include error string definitions for the keys 'login_failed' and 'invalid_auth' under the config flow errors section.


*   Interface details: Type: Method
Name: async_step_user
Location: homeassistant/components/ecobee/config_flow.py
Signature: async_step_user(self, user_input=None) -> FlowResult
Description: Handles the user step of the ecobee config flow. Must detect when CONF_USERNAME and CONF_PASSWORD are present in user_input (with or without CONF_API_KEY) and attempt credential-based authentication. When only CONF_API_KEY is provided, falls back to the existing PIN-based flow. On credential auth success (refresh_tokens returns True), creates an entry with title=DOMAIN and data={CONF_USERNAME: username, CONF_PASSWORD: password, CONF_REFRESH_TOKEN: ecobee.refresh_token}. On failure with username+password only, returns to user form with errors={"base": "login_failed"}. On failure with api_key+username+password, returns to user form with errors={"base": "invalid_auth"}.

Type: Config
Name: strings.json error keys
Location: homeassistant/components/ecobee/strings.json
Description: The strings.json file must define error entries for the keys "login_failed" and "invalid_auth" under the config flow errors section, so Home Assistant can display localized error messages for credential authentication failures.

Type: Constant usage note
Name: Ecobee constructor config keys
Location: homeassistant/components/ecobee/config_flow.py
Description: When instantiating the Ecobee class for credential-based authentication, the config dict must use ECOBEE_USERNAME and ECOBEE_PASSWORD as keys (imported from pyecobee), mapped to the user-provided CONF_USERNAME and CONF_PASSWORD values respectively. The call pattern is: Ecobee(config={ECOBEE_USERNAME: username, ECOBEE_PASSWORD: password}).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.