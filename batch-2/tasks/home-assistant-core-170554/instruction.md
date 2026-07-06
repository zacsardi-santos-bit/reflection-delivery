I'm working on the Xthings Cloud integration for Home Assistant and I need to add support for smart plug and switch-type devices.

*   A new switch platform module must be created at homeassistant/components/xthings_cloud/switch.py, implementing async_setup_entry and the XthingsCloudSwitch entity class.

*   The async_setup_entry function must create XthingsCloudSwitch entities only for devices whose type field is 'switch' or 'plug'; devices of other types must not appear as switch entities.

*   XthingsCloudSwitch must inherit from XthingsCloudEntity and SwitchEntity (in that order). Its is_on property must return the boolean value of device_data['status']['on'].

*   When turn_on is called on a device of type 'plug', the integration must call async_plug_on(device_id) on the coordinator's API client. When turn_off is called on a 'plug' device, it must call async_plug_off(device_id).

*   When turn_on is called on a device of type 'switch', the integration must call async_switch_on(device_id) on the coordinator's API client. When turn_off is called on a 'switch' device, it must call async_switch_off(device_id).

*   If a device's 'online' field is False, the corresponding switch entity must report STATE_UNAVAILABLE.

*   Switch entity states must update in real time when the websocket on_device_status callback is invoked with a device ID and a status dict (e.g., {'on': True}); the entity's state must reflect the new value immediately.

*   Switch entities must be registered with domain='switch', platform='xthings_cloud', has_entity_name=True, unique_id equal to the device ID, and supported_features=0.

*   The PLATFORMS constant in homeassistant/components/xthings_cloud/const.py must include Platform.SWITCH (alongside the existing Platform.LIGHT), and must be accessible at the module path homeassistant.components.xthings_cloud.PLATFORMS.


*   Interface details: Type: Function
Name: async_setup_entry
Location: homeassistant/components/xthings_cloud/switch.py
Signature: async_setup_entry(hass: HomeAssistant, entry: XthingsCloudConfigEntry, async_add_entities: AddConfigEntryEntitiesCallback) -> None
Description: Platform setup entry point for the switch platform. Creates XthingsCloudSwitch entities for all devices in coordinator.data whose "type" field is either "switch" or "plug", then registers them via async_add_entities.

Type: Class
Name: XthingsCloudSwitch
Location: homeassistant/components/xthings_cloud/switch.py
Description: Switch entity for Xthings Cloud devices. Inherits from both XthingsCloudEntity and SwitchEntity (in that order). Implements the following methods:
  - is_on (property) -> bool: Returns self.device_data["status"]["on"]
  - async_turn_on(**kwargs) -> None: If self.device_data["type"] == "plug", calls self.coordinator.client.async_plug_on(self._device_id); otherwise calls self.coordinator.client.async_switch_on(self._device_id)
  - async_turn_off(**kwargs) -> None: If self.device_data["type"] == "plug", calls self.coordinator.client.async_plug_off(self._device_id); otherwise calls self.coordinator.client.async_switch_off(self._device_id)

Type: Constant
Name: PLATFORMS
Location: homeassistant/components/xthings_cloud/const.py
Signature: PLATFORMS: list[Platform] = [Platform.LIGHT, Platform.SWITCH]
Description: The list of platforms supported by the xthings_cloud integration. Must include Platform.SWITCH in addition to Platform.LIGHT so that the switch platform is loaded. The constant must be importable as homeassistant.components.xthings_cloud.PLATFORMS (exported from __init__.py or const.py, accessible at that path).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.