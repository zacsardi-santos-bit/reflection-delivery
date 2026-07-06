I'm working on the WaterFurnace geothermal heat pump integration for Home Assistant.

*   The PLATFORMS list in homeassistant/components/waterfurnace/__init__.py must include Platform.CLIMATE so the climate entity is loaded when the integration is set up.

*   A new climate platform file must exist at homeassistant/components/waterfurnace/climate.py and must register a climate entity for the WaterFurnace device with: entity_id based on the device name, unique_id equal to the device GWID, has_entity_name=True, supported HVAC modes [OFF, HEAT, COOL, HEAT_COOL], min_temp=4.4°C, max_temp=26.7°C, min_humidity=15, max_humidity=95, and ClimateEntityFeature flags totaling 391.

*   The current HVAC mode must be derived from the activesettings.activemode integer field: 0→HVACMode.OFF, 1→HVACMode.HEAT_COOL, 2→HVACMode.COOL, 3→HVACMode.HEAT, 4→HVACMode.HEAT (E-Heat also maps to HEAT).

*   The current HVAC action must be derived from the modeofoperation integer field: 0→HVACAction.IDLE, 1→HVACAction.FAN, 2→HVACAction.COOLING, 3→HVACAction.COOLING, 4→HVACAction.HEATING, 5→HVACAction.HEATING, 6→HVACAction.HEATING, 7→HVACAction.HEATING, 8→HVACAction.HEATING, 9→HVACAction.OFF.

*   Setting the HVAC mode must call client.set_mode() with the mapped integer: HVACMode.OFF→0, HVACMode.HEAT_COOL→1, HVACMode.COOL→2, HVACMode.HEAT→3. If the underlying library raises an error, raise a HomeAssistantError with message matching 'Failed to set HVAC mode'.

*   Setting a single temperature target must call client.set_heating_setpoint(fahrenheit) when in HEAT mode (and not call set_cooling_setpoint), and call client.set_cooling_setpoint(fahrenheit) when in COOL mode (and not call set_heating_setpoint). Temperature values are converted from Celsius to Fahrenheit before calling the library. If the library raises an error, raise a HomeAssistantError with message matching 'Failed to set temperature'.

*   Setting a temperature range (low/high) in HEAT_COOL mode must call both client.set_heating_setpoint(low_fahrenheit) and client.set_cooling_setpoint(high_fahrenheit).

*   If the set_temperature service call includes an HVAC mode parameter, the climate entity must first switch the mode (calling client.set_mode() with the appropriate integer) before setting the temperature setpoint(s) according to the new mode.

*   Setting humidity must call client.set_humidity(value) with the integer humidity value. If the library raises an error, raise a HomeAssistantError with message matching 'Failed to set humidity'.

*   In HEAT or COOL mode, the temperature attribute must return the single setpoint converted from Fahrenheit to Celsius (tstatheatingsetpoint in HEAT mode, tstatcoolingsetpoint in COOL mode), and target_temp_low / target_temp_high must be None. In HEAT_COOL mode, temperature must be None and target_temp_low / target_temp_high must be the heating and cooling setpoints (respectively) converted from Fahrenheit to Celsius.

*   The sensor for the dehumidification setpoint must read from the tstatdehumidsetpoint device data field and report its integer value directly as the sensor state. The sensor for the heating setpoint and the sensor for the cooling setpoint must read from tstatheatingsetpoint and tstatcoolingsetpoint respectively, converting from Fahrenheit to Celsius.


*   Interface details: Type: Variable
Name: PLATFORMS
Location: homeassistant/components/waterfurnace/__init__.py
Description: Module-level list of Platform values that the WaterFurnace integration registers. Must include both Platform.CLIMATE and Platform.SENSOR. Tests patch this path (homeassistant.components.waterfurnace.PLATFORMS) to control which platforms load during testing.

Type: Function
Name: async_setup_entry
Location: homeassistant/components/waterfurnace/climate.py
Signature: async_setup_entry(hass: HomeAssistant, config_entry: ConfigEntry, async_add_entities: AddEntitiesCallback) -> None
Description: Entry point for setting up the WaterFurnace climate platform. Must create and register the climate entity for the integration.

Type: Class
Name: WaterFurnaceClimate (or any name; accessed via HA service dispatch)
Location: homeassistant/components/waterfurnace/climate.py
Description: Climate entity representing the WaterFurnace geothermal heat pump. Must extend ClimateEntity and implement the following:
  - hvac_mode: returns current mode derived from activesettings.activemode (0→OFF, 1→HEAT_COOL, 2→COOL, 3→HEAT, 4→HEAT)
  - hvac_action: returns current action derived from modeofoperation (0→IDLE, 1→FAN, 2→COOLING, 3→COOLING, 4→HEATING, 5→HEATING, 6→HEATING, 7→HEATING, 8→HEATING, 9→OFF)
  - hvac_modes: [HVACMode.OFF, HVACMode.HEAT, HVACMode.COOL, HVACMode.HEAT_COOL]
  - min_temp=4.4, max_temp=26.7 (Celsius)
  - min_humidity=15, max_humidity=95
  - supported_features: ClimateEntityFeature flags totaling 391
  - has_entity_name=True
  - unique_id: device GWID
  - temperature (single target): heating setpoint in HEAT mode, cooling setpoint in COOL mode, None in HEAT_COOL mode; values converted from °F to °C
  - target_temp_low / target_temp_high: heating/cooling setpoints in HEAT_COOL mode, None otherwise
  - async_set_hvac_mode(hvac_mode): calls client.set_mode(int) with OFF→0, HEAT_COOL→1, COOL→2, HEAT→3; raises HomeAssistantError("Failed to set HVAC mode: ...") on WFException
  - async_set_temperature(**kwargs): converts °C to °F; in HEAT mode calls set_heating_setpoint only; in COOL mode calls set_cooling_setpoint only; in HEAT_COOL mode calls both; if ATTR_HVAC_MODE present, calls set_mode first; raises HomeAssistantError("Failed to set temperature: ...") on WFException
  - async_set_humidity(humidity): calls client.set_humidity(value); raises HomeAssistantError("Failed to set humidity: ...") on WFException


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.