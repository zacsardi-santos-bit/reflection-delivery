I'm working on the SMLIGHT integration for Home Assistant and I'd like to add support for controlling the built-in ambient LED strip available on SMLIGHT Ultima devices.

*   Platform.LIGHT must be added to the PLATFORMS list in homeassistant/components/smlight/__init__.py so the light platform is loaded for SMLIGHT integrations.

*   A new light entity must be created at homeassistant/components/smlight/light.py for SMLIGHT Ultima devices (devices that have peripheral LED hardware). No light entity should be created for non-Ultima devices.

*   The light entity must have: unique_id formatted as '{coordinator_unique_id}-ambilight' (hyphen-separated), translation_key 'ambilight', icon 'mdi:led-strip', entity_category None, color mode RGB, and LightEntityFeature.EFFECT support.

*   The entity's effect_list must contain exactly these 17 effects in this order: ['Solid', 'Off', 'Blur', 'Rainbow', 'Breathing', 'Color Wipe', 'Comet', 'Fire', 'Twinkle', 'Police', 'Chase', 'Color Cycle', 'Gradient Scroll', 'Strobe', 'System Warning', 'System Error', 'System OK', 'System Info']. These correspond to the AMBI_EFFECT_LIST constant from the pysmlight library.

*   The strings.json translation file must include an entry for the light entity under the 'light' domain key with name 'Ambilight' for translation_key 'ambilight'.

*   When turn_on is called with no keyword arguments and the light is currently off, the implementation must call api.actions.ambilight with AmbilightPayload(ultLedMode=AmbiEffect.WSULT_SOLID).

*   When turn_on is called with no keyword arguments and the light is currently on, no API call must be made (noop).

*   When turn_off is called, the implementation must call api.actions.ambilight with AmbilightPayload(ultLedMode=AmbiEffect.WSULT_OFF).

*   When turn_on is called with only a brightness value and the light is already on, the implementation must call api.actions.ambilight with AmbilightPayload(ultLedBri=<brightness>) — no mode change.

*   When turn_on is called with an RGB color tuple (r, g, b), the implementation must call api.actions.ambilight with AmbilightPayload(ultLedMode=AmbiEffect.WSULT_SOLID, ultLedColor='#rrggbb') where ultLedColor is a lowercase hex string.

*   When turn_on is called with a named effect that exists in the effect list, the implementation must call api.actions.ambilight with AmbilightPayload(ultLedMode=<AmbiEffect>) corresponding to the effect's index in the list. When the effect name is not in the list, no API call must be made.

*   The entity must register an SSE page callback (via sse.register_page_cb) to receive real-time updates. On each update: ultLedMode=None or ultLedMode matching AmbiEffect.WSULT_OFF sets state to off; any other valid mode sets state to on; an unknown/out-of-range integer mode sets state to on with no effect; ultLedBri sets brightness; an integer ultLedColor value must be converted to a '#rrggbb' hex string before parsing to an rgb tuple; an invalid hex color string results in rgb_color=None.

*   When api.actions.ambilight raises SmlightConnectionError, the service call must raise HomeAssistantError. The entity must recover and accept subsequent successful commands after the error is cleared.


*   Interface details: Type: File
Name: light.py
Location: homeassistant/components/smlight/light.py
Description: New light platform file for the SMLIGHT integration. Must implement async_setup_entry and provide the SmLightEntity class.

Type: Function
Name: async_setup_entry
Location: homeassistant/components/smlight/light.py
Signature: async_setup_entry(hass: HomeAssistant, entry: SmConfigEntry, async_add_entities: AddConfigEntryEntitiesCallback) -> None
Description: Platform setup function. Must only create the ambilight light entity when the device has peripheral LED hardware (coordinator.data.info.has_peripherals is True). For non-Ultima devices, no entity is created.

Type: Class
Name: SmLightEntity
Location: homeassistant/components/smlight/light.py
Description: Light entity for the SMLIGHT Ultima ambilight LED strip. Must inherit from SmEntity and LightEntity.
Attributes:
  - _attr_unique_id: formatted as f"{coordinator.unique_id}-ambilight" (hyphen separator)
  - translation_key: "ambilight"
  - icon: "mdi:led-strip"
  - _attr_supported_color_modes: {ColorMode.RGB}
  - _attr_color_mode: ColorMode.RGB
  - _attr_supported_features: LightEntityFeature.EFFECT
  - _attr_effect_list: the 17-item AMBI_EFFECT_LIST constant from pysmlight.const

Methods:
  async_turn_on(**kwargs) -> None:
    - If ATTR_EFFECT in kwargs and effect name is in effect_list: send AmbilightPayload(ultLedMode=<AmbiEffect matching index>)
    - If ATTR_EFFECT in kwargs and effect name is NOT in effect_list: do nothing (return early)
    - If no ATTR_EFFECT and light is currently off: send AmbilightPayload(ultLedMode=AmbiEffect.WSULT_SOLID)
    - If ATTR_BRIGHTNESS in kwargs: set payload.ultLedBri = brightness value
    - If ATTR_RGB_COLOR in kwargs: set payload.ultLedMode=AmbiEffect.WSULT_SOLID and payload.ultLedColor as lowercase hex string "#rrggbb"
    - If resulting payload is empty (equals AmbilightPayload()): do nothing
    - Calls api.actions.ambilight(payload)
    - Converts SmlightConnectionError to HomeAssistantError

  async_turn_off(**kwargs) -> None:
    - Calls api.actions.ambilight(AmbilightPayload(ultLedMode=AmbiEffect.WSULT_OFF))
    - Converts SmlightConnectionError to HomeAssistantError

  async_added_to_hass() -> None:
    - Registers SSE page callback via coordinator.client.sse.register_page_cb for ambilight page events

  State update handler (called when SSE ambilight events arrive):
    - ultLedMode=None → is_on=False, effect=None, rgb_color=None
    - ultLedMode=AmbiEffect.WSULT_OFF (value 1) → is_on=False
    - Any other valid AmbiEffect index → is_on=True, effect=effect_list[mode]
    - Out-of-range integer mode → is_on=True, effect=None
    - Integer ultLedColor (e.g., 0x7FACFF) must be converted to hex string "#7facff" before parsing
    - Invalid hex color string (e.g., "#GG0000") → rgb_color=None
    - ultLedBri → brightness attribute

Type: Configuration
Name: PLATFORMS
Location: homeassistant/components/smlight/__init__.py
Description: Platform.LIGHT must be added to the PLATFORMS list.

Type: Configuration
Name: strings.json
Location: homeassistant/components/smlight/strings.json
Description: Must include a translation entry for the light entity under "entity" > "light" > "ambilight" with name "Ambilight".

Type: Method
Name: update_ambilight
Location: homeassistant/components/smlight/coordinator.py (SmDataUpdateCoordinator)
Signature: update_ambilight(self, changes: dict) -> None
Description: Processes raw SSE ambilight change events. Must convert integer values of 'ultLedColor' and 'ultLedColor2' keys to hex strings using "#%06x" format, then update coordinator.data.sensors.ambilight with AmbilightPayload(**changes) and notify listeners via async_set_updated_data.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.