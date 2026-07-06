I'm working on the Android TV integration in Home Assistant and I'd like to improve the setup experience.

*   Must define a new constant `CONF_MORE_OPTIONS` in `homeassistant/components/androidtv/const.py`, importable from `homeassistant.components.androidtv.const`.

*   The config flow user step in `homeassistant/components/androidtv/config_flow.py` must accept user input that includes a `CONF_MORE_OPTIONS` key whose value is a dict optionally containing `CONF_ADBKEY`, `CONF_ADB_SERVER_IP`, and/or `CONF_ADB_SERVER_PORT`.

*   When processing user input that contains a `CONF_MORE_OPTIONS` key, the flow must merge the nested dict contents into the top-level data and store the flattened result in the config entry — the config entry data must not contain a `CONF_MORE_OPTIONS` key.

*   The config flow user step must present and accept the full set of connection options (including ADB key and ADB server fields) without requiring `show_advanced_options` to be set in the flow context.

*   All existing error-handling paths (both ADB key and server provided, invalid key, connection failure, invalid MAC) must continue to work correctly when receiving user input in the nested `CONF_MORE_OPTIONS` format.


*   Interface details: Type: Constant
Name: CONF_MORE_OPTIONS
Location: homeassistant/components/androidtv/const.py
Description: String constant used as the key for the "more options" section in the config flow user step. Must be importable from `homeassistant.components.androidtv.const`. The tests also import it from `homeassistant.components.androidtv.const` in the test file.

Type: Class
Name: AndroidTVFlowHandler
Location: homeassistant/components/androidtv/config_flow.py
Description: The existing config flow handler for the Android TV integration. Its user step (`async_step_user`) must be updated to:
  1. Present ADB connection options (ADB key, ADB server IP, ADB server port) as fields nested under a `CONF_MORE_OPTIONS` section key in the user step schema, rather than as a conditional extension based on `show_advanced_options`.
  2. Before processing submitted user input, extract the value of `CONF_MORE_OPTIONS` from the input dict, merge its contents into the top-level input dict, and proceed with the flattened dict. The stored config entry data must not contain `CONF_MORE_OPTIONS`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.