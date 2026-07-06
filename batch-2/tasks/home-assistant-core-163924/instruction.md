I'm working on a Bluetooth lighting integration and I'd like to rework how users set it up.

*   The config flow for the Hue BLE integration must implement a user-initiated setup step (async_step_user) in the HueBleConfigFlow class located at homeassistant/components/hue_ble/config_flow.py.

*   When the user step is initialized with no input, it must call bluetooth.async_discovered_service_info to retrieve nearby Bluetooth devices, filter them to only include Hue BLE compatible devices (those whose advertisement data contains the Hue service UUID in both service_uuids and service_data fields), and return a FORM result with step_id 'user'.

*   The user step form must include a CONF_MAC field whose schema is a vol.In selector mapping each discovered device's address to a display string formatted as '{name} ({address})'.

*   If no compatible Hue BLE devices are found (either because no devices are discovered or none pass the device filter), the user step must abort with reason 'no_devices_found'.

*   When the user submits a MAC address selection in the user step, the flow must set the unique ID for that device and transition to the confirm step (async_step_confirm). If the selected device is already configured, it must abort with reason 'already_configured'.

*   The Bluetooth auto-discovery step (async_step_bluetooth) must be changed to abort immediately with reason 'discovery_unsupported' instead of starting a pairing flow.

*   The strings.json file must define two abort reasons: 'discovery_unsupported' (indicating the Bluetooth discovery flow is not supported) and 'no_devices_found' (using the common key), replacing the old 'not_implemented' abort reason.

*   The strings.json file must also define a 'user' step entry with a 'mac' data field (labeled using the common device key) and a data description.


*   Interface details: Type: Class Method
Name: async_step_user
Location: homeassistant/components/hue_ble/config_flow.py
Signature: async_step_user(self, user_input: dict[str, Any] | None = None) -> ConfigFlowResult
Description: New method on HueBleConfigFlow. When called with no user_input, queries bluetooth.async_discovered_service_info(self.hass) to discover nearby BLE devices, filters them using device_filter, and returns a FORM with step_id="user" and a data_schema containing a CONF_MAC field mapped to a vol.In selector. The selector's container maps each device address to the string "{name} ({address})". Aborts with reason "no_devices_found" if no compatible devices exist. When called with user_input containing CONF_MAC, sets unique ID and transitions to async_step_confirm, aborting with "already_configured" if already set up.

Type: Function
Name: device_filter
Location: homeassistant/components/hue_ble/config_flow.py
Signature: device_filter(advertisement_data: AdvertisementData) -> bool
Description: Returns True if the advertisement data indicates a Hue BLE compatible device. Must check that the Hue BLE service UUID ("0000fe0f-0000-1000-8000-00805f9b34fb") is present in both advertisement_data.service_uuids and advertisement_data.service_data. Used by async_step_user to filter discovered devices.

Type: Class Method (modified)
Name: async_step_bluetooth
Location: homeassistant/components/hue_ble/config_flow.py
Signature: async_step_bluetooth(self, discovery_info: bluetooth.BluetoothServiceInfoBleak) -> ConfigFlowResult
Description: Modified to immediately abort with reason "discovery_unsupported" instead of starting a pairing flow. This applies regardless of whether the device is already configured.

Type: Strings File
Name: strings.json
Location: homeassistant/components/hue_ble/strings.json
Description: Must be updated to:
- Remove the "not_implemented" abort reason
- Add abort reason "discovery_unsupported" (e.g., "Discovery flow is not supported by the Hue BLE integration.")
- Add abort reason "no_devices_found" (using common key reference)
- Add a "user" step entry under "step" with:
  - "data": {"mac": "<common device label key>"} 
  - "data_description": {"mac": "Select the Hue device you want to set up"}
  - "description": <reference to common bluetooth user step description>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.