I'm working on the Aladdin Connect garage door integration for Home Assistant, and I notice it doesn't have diagnostics support yet.

*   Must create a new file homeassistant/components/aladdin_connect/diagnostics.py that exposes an async_get_config_entry_diagnostics function following the standard Home Assistant diagnostics protocol.

*   The async_get_config_entry_diagnostics function must accept hass (HomeAssistant) and config_entry (the Aladdin Connect config entry type) as parameters and return a dict[str, Any].

*   The returned dict must contain a 'config_entry' key whose value is the config entry's serialized data with the 'access_token' and 'refresh_token' fields replaced by the string '**REDACTED**'.

*   The returned dict must contain a 'doors' key whose value is a dictionary of garage door state data, keyed by each door's unique identifier in the format '{device_id}-{door_number}' (e.g., 'test_device_id-1').

*   Each door entry in the 'doors' dict must contain exactly these fields: 'device_id' (string), 'door_number' (integer), 'name' (string), 'status' (string), 'link_status' (string), and 'battery_level' (integer).

*   When the diagnostics endpoint is requested for the aladdin_connect config entry, it must respond with HTTP 200 OK and return the structured diagnostics data described above.


*   Interface details: Type: Function
Name: async_get_config_entry_diagnostics
Location: homeassistant/components/aladdin_connect/diagnostics.py
Signature: async_get_config_entry_diagnostics(hass: HomeAssistant, config_entry: AladdinConnectConfigEntry) -> dict[str, Any]
Description: Returns diagnostics data for the Aladdin Connect config entry. Must return a dict with two keys: "config_entry" (the serialized config entry data with sensitive token fields redacted) and "doors" (a dict of door state data keyed by the door's unique identifier). The sensitive fields that must be redacted are "access_token" and "refresh_token" — they must appear as "**REDACTED**" in the output. Each door entry in "doors" must be keyed by its unique ID (formatted as "{device_id}-{door_number}") and contain the fields: "device_id", "door_number", "name", "status", "link_status", and "battery_level".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.