I'm working on the Duco ventilation integration for Home Assistant and I'd like to add diagnostics support.

*   The async_get_config_entry_diagnostics function must be defined in homeassistant/components/duco/diagnostics.py and accept a HomeAssistant instance and a DucoConfigEntry as parameters, returning a dict.

*   The function must call client.async_get_diagnostics() (which returns a list of DiagComponent objects) and client.async_get_write_req_remaining() (which returns an integer).

*   The returned dictionary must contain exactly these top-level keys: 'board_info', 'duco_diagnostics', 'entry_data', 'lan_info', 'nodes', and 'write_requests_remaining'.

*   The 'duco_diagnostics' value must be a list of dicts, each containing 'component' (string) and 'status' (string) fields derived from the DiagComponent objects returned by the client.

*   The 'write_requests_remaining' value must be the integer returned by client.async_get_write_req_remaining().

*   The 'nodes' value must be a dict keyed by string representations of node IDs, where each value is a dict containing 'general', 'node_id', 'sensor', and 'ventilation' sub-fields.

*   The following sensitive fields must be redacted (shown as '**REDACTED**') in the output: 'serial_board_box', 'serial_board_comm', 'serial_duco_box', 'serial_duco_comm' (in board_info); 'host' (in entry_data); 'host_name' and 'mac' (in lan_info).

*   The 'board_info' dict must include 'box_name' and 'box_sub_type_name' in addition to the redacted serial fields.

*   The 'lan_info' dict must include 'default_gateway', 'dns', 'ip', 'mode', 'net_mask', and 'rssi_wifi' in addition to the redacted 'host_name' and 'mac' fields.


*   Interface details: Type: Function
Name: async_get_config_entry_diagnostics
Location: homeassistant/components/duco/diagnostics.py
Signature: async_get_config_entry_diagnostics(hass: HomeAssistant, entry: DucoConfigEntry) -> dict[str, Any]
Description: Returns diagnostics data for a Duco config entry. Must retrieve LAN info, diagnostics component list, and write-requests-remaining from the client, then return a redacted dictionary with keys: "entry_data", "board_info", "lan_info", "nodes", "duco_diagnostics", and "write_requests_remaining". Sensitive fields must be redacted using Home Assistant's async_redact_data utility.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.