I have a Roborock Q10 S5+ robot vacuum that uses a newer push-based protocol, and the Home Assistant Roborock integration doesn't support it.

*   A new vacuum entity must be created for Roborock Q10 devices (identified by model 'roborock.vacuum.ss07'), with entity_id 'vacuum.roborock_q10_s5', unique_id equal to the device duid ('q10_duid'), platform 'roborock', and translation_key 'roborock'.

*   The Q10 vacuum entity must report a supported_features value of VacuumEntityFeature 13116 (the combination of PAUSE, STOP, RETURN_HOME, FAN_SPEED, SEND_COMMAND, LOCATE, STATE, and START features).

*   The Q10 vacuum entity must expose fan_speed_list as ['off', 'quiet', 'balanced', 'turbo', 'max', 'max_plus'].

*   The Q10 vacuum state must map device states to Home Assistant VacuumActivity as follows: CHARGING_STATE → docked, CLEANING_STATE (datapoint code 5) → cleaning, TO_CHARGE_STATE (datapoint code 6) → returning, None status → unknown HA state.

*   The start command must call api.vacuum.start_clean() with no positional arguments. The pause command must call api.vacuum.pause_clean() with no positional arguments. The stop command must call api.vacuum.stop_clean() with no positional arguments. The return_to_base command must call api.vacuum.return_to_dock() with no positional arguments.

*   The locate command must call api.command.send() with B01_Q10_DP.SEEK as the sole positional argument.

*   The set_fan_speed command must resolve the fan speed string to a YXFanLevel enum value and call api.vacuum.set_fan_level() with that value. An unrecognized fan speed string must raise ServiceValidationError without calling the API.

*   The send_command service must accept a command specified as an enum member name (e.g. 'SEEK'), a DP string value (e.g. 'dpSeek'), or an integer code as a string (e.g. '11'), and must call api.command.send() exactly once. An unrecognized command string must raise ServiceValidationError without calling the API.

*   Any RoborockException raised by the device API during any vacuum command (start, pause, stop, return_to_base, locate, set_fan_speed, send_command) must be caught and re-raised as HomeAssistantError.

*   The Q10 entity must subscribe to push-based status updates from the device. When api.status.update_from_dps() is called with a new device state, the entity state must update accordingly without polling.

*   When the Home Assistant update_entity service is invoked on the Q10 vacuum, api.refresh() must be called. The entity state is not changed immediately (refresh is fire-and-forget).

*   The RoborockCoordinators data structure must include a b01_q10 field that is a list of RoborockB01Q10UpdateCoordinator instances. This field must be accessible at config_entry.runtime_data.b01_q10.

*   The Q10 device must appear in the diagnostics output alongside other devices, with sensitive fields (name, localKey, sn, productId, duid) redacted.


*   Interface details: Type: Class
Name: RoborockB01Q10UpdateCoordinator
Location: homeassistant/components/roborock/coordinator.py
Description: DataUpdateCoordinator for B01 Q10 devices. Uses push-based MQTT status updates; _async_update_data() calls api.refresh() (fire-and-forget). Exposes duid, duid_slug, device, device_info, and api attributes.
Signature: __init__(self, hass: HomeAssistant, config_entry: RoborockConfigEntry, device: RoborockDevice, api: Q10PropertiesApi) -> None

Type: Class
Name: RoborockCoordinatedEntityB01Q10
Location: homeassistant/components/roborock/entity.py
Description: Base entity class for coordinated Roborock Q10 entities. Inherits from both RoborockEntity and CoordinatorEntity[RoborockB01Q10UpdateCoordinator].
Signature: __init__(self, unique_id: str, coordinator: RoborockB01Q10UpdateCoordinator) -> None

Type: Class
Name: RoborockQ10Vacuum
Location: homeassistant/components/roborock/vacuum.py
Description: Vacuum entity for Roborock Q10 devices. Inherits from RoborockCoordinatedEntityB01Q10 and StateVacuumEntity. Supports fan speeds ['off', 'quiet', 'balanced', 'turbo', 'max', 'max_plus']. Supported features value is VacuumEntityFeature 13116. Subscribes to push status updates from api.status. Commands: async_start() calls api.vacuum.start_clean(), async_pause() calls api.vacuum.pause_clean(), async_stop() calls api.vacuum.stop_clean(), async_return_to_base() calls api.vacuum.return_to_dock(), async_locate() calls api.command.send(B01_Q10_DP.SEEK), async_set_fan_speed() calls api.vacuum.set_fan_level(YXFanLevel), async_send_command() calls api.command.send(). Invalid fan speed raises ServiceValidationError. Invalid command raises ServiceValidationError. RoborockException from any command raises HomeAssistantError.
Signature: __init__(self, coordinator: RoborockB01Q10UpdateCoordinator) -> None

Type: Dataclass field
Name: b01_q10
Location: homeassistant/components/roborock/coordinator.py (RoborockCoordinators dataclass)
Description: Field on the RoborockCoordinators dataclass holding a list of RoborockB01Q10UpdateCoordinator instances. Accessed at config_entry.runtime_data.b01_q10.
Signature: b01_q10: list[RoborockB01Q10UpdateCoordinator]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.