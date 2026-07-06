I'm building a Home Assistant integration for Zinvolt smart battery storage systems and need help implementing it from scratch.

*   The integration must define a DOMAIN constant equal to 'zinvolt' in homeassistant/components/zinvolt/const.py.

*   The config flow must present an initial user step with a form containing email and password fields; the initial form must return with empty errors.

*   The config flow must use the Zinvolt client to perform login with the provided email and password, and on success store the returned access token in the config entry data under the CONF_ACCESS_TOKEN key.

*   The config entry title must be set to the user's email address.

*   The config entry unique ID must be extracted from the JWT access token's 'sub' claim (the user identifier embedded in the token payload).

*   The config flow must map ZinvoltAuthenticationError to the base error 'invalid_auth', ZinvoltError to 'cannot_connect', and any other exception to 'unknown'; the form must allow re-submission after an error.

*   If a config entry with the same unique ID already exists, the config flow must abort with reason 'already_configured'.

*   During integration setup, the client must call get_batteries() to retrieve the list of batteries and get_battery_status() with the battery identifier to retrieve the current battery state.

*   A device registry entry must be created for each battery with: identifiers set to {(DOMAIN, serial_number)}, manufacturer set to 'Zinvolt', name set to the battery's name, and serial_number set to the battery's serial number.

*   A sensor entity for battery state of charge must be provided with: unique_id in the format '{serial_number}.state_of_charge', device class BATTERY, entity category DIAGNOSTIC, native unit of measurement '%', has_entity_name set to True, no explicit name or translation_key set (the name 'Battery' is derived from the device class automatically), and state value equal to the soc field from currentPower reported as a float.

*   The integration module must expose a _PLATFORMS list that includes at least the SENSOR platform, and must expose ZinvoltClient so it can be patched at the module level ('homeassistant.components.zinvolt.ZinvoltClient').

*   The config flow module must also import ZinvoltClient directly so it can be patched at the config_flow module level ('homeassistant.components.zinvolt.config_flow.ZinvoltClient').


*   Interface details: Type: Constant
Name: DOMAIN
Location: homeassistant/components/zinvolt/const.py
Signature: DOMAIN: str = "zinvolt"
Description: The integration domain identifier string used throughout the integration and device registry.

Type: Variable
Name: _PLATFORMS
Location: homeassistant/components/zinvolt/__init__.py
Signature: _PLATFORMS: list[Platform]
Description: List of platforms this integration supports. Must include at least Platform.SENSOR. Tests patch this variable to restrict platform loading during setup.

Type: Function
Name: async_setup_entry
Location: homeassistant/components/zinvolt/__init__.py
Signature: async_setup_entry(hass: HomeAssistant, entry: ConfigEntry) -> bool
Description: Sets up the Zinvolt integration from a config entry. Uses ZinvoltClient to call get_batteries() and get_battery_status() to load data. Registers a device in the device registry with identifiers={(DOMAIN, serial_number)}, manufacturer="Zinvolt", name=battery.name, serial_number=battery.serial_number. Forwards setup to all platforms in _PLATFORMS.

Type: Import
Name: ZinvoltClient
Location: homeassistant/components/zinvolt/__init__.py
Description: The ZinvoltClient class from the zinvolt library must be imported at the homeassistant.components.zinvolt module level so tests can patch "homeassistant.components.zinvolt.ZinvoltClient".

Type: Import
Name: ZinvoltClient
Location: homeassistant/components/zinvolt/config_flow.py
Description: The ZinvoltClient class from the zinvolt library must be imported at the homeassistant.components.zinvolt.config_flow module level so tests can patch "homeassistant.components.zinvolt.config_flow.ZinvoltClient".

Type: Class
Name: ConfigFlow subclass (e.g. ZinvoltConfigFlow)
Location: homeassistant/components/zinvolt/config_flow.py
Description: Handles the user-initiated configuration flow registered for domain "zinvolt". The async_step_user method presents a form with CONF_EMAIL and CONF_PASSWORD fields and empty initial errors. It calls ZinvoltClient.login(email, password) to authenticate. On success: decodes the JWT token to extract the 'sub' claim as the unique_id, calls async_set_unique_id(), aborts with "already_configured" if a duplicate exists, and creates an entry with title=email and data={CONF_ACCESS_TOKEN: token}. Error handling maps ZinvoltAuthenticationError -> {"base": "invalid_auth"}, ZinvoltError -> {"base": "cannot_connect"}, and any other Exception -> {"base": "unknown"}.

Type: Class
Name: SensorEntity subclass (e.g. ZinvoltBatteryStateSensor)
Location: homeassistant/components/zinvolt/sensor.py
Description: Sensor entity for battery state of charge. Required attributes: _attr_unique_id="{serial_number}.state_of_charge" (using the description key "state_of_charge"); device_class=SensorDeviceClass.BATTERY; entity_category=EntityCategory.DIAGNOSTIC; native_unit_of_measurement="%" (PERCENTAGE); _attr_has_entity_name=True; NO explicit name or translation_key set (translation_key must be None in the registry — the name "Battery" is derived automatically from SensorDeviceClass.BATTERY); native_value returns the soc field from currentPower as a float (e.g. 4.0).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.