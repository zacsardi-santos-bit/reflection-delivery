I'm working on a Home Assistant integration for Fresh-r ventilation devices and I'd like to add diagnostics support to it.

*   The freshr component must implement a diagnostics module located at homeassistant/components/freshr/diagnostics.py that provides the async_get_config_entry_diagnostics function.

*   The async_get_config_entry_diagnostics function must return a dict with three top-level keys: 'entry', 'devices', and 'readings'.

*   The 'entry' key must contain the config entry diagnostic data with the 'password' field redacted (shown as '**REDACTED**') while other fields like 'username' are preserved as-is.

*   The 'devices' key must be a list of device dicts, where each device dict contains at least the following fields: 'active_from', 'extras', 'id', and 'type'.

*   The 'readings' key must be a dict keyed by device serial number (matching the device 'id'), where each reading dict contains at minimum: 'co2', 'dp', 'extras', 'flow', 'hum', 't1', 't2', 't3', 't4', and 'temp' fields.


*   Interface details: Type: Function
Name: async_get_config_entry_diagnostics
Location: homeassistant/components/freshr/diagnostics.py
Signature: async_get_config_entry_diagnostics(hass: HomeAssistant, entry: ConfigEntry) -> dict[str, Any]
Description: Returns diagnostics data for a Fresh-r config entry. The return dict must have three keys: 'entry' (config entry data with password redacted), 'devices' (list of device dicts with 'active_from', 'extras', 'id', 'type' fields), and 'readings' (dict keyed by device serial number, each value containing 'co2', 'dp', 'extras', 'flow', 'hum', 't1', 't2', 't3', 't4', 'temp' fields).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.