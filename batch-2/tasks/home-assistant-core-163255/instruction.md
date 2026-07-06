I'm working on the Smarla baby rocker integration for Home Assistant and I'd like to add support for firmware updates.

*   The PLATFORMS list in homeassistant/components/smarla/const.py must include Platform.UPDATE so the update platform is loaded for the smarla integration.

*   A new file homeassistant/components/smarla/update.py must be created with an async_setup_entry function that registers the firmware update entity.

*   The update entity must be registered with entity_id 'update.smarla_firmware', entity_category CONFIG, device_class 'firmware' (UpdateDeviceClass.FIRMWARE), original_name 'Firmware', unique_id in the format '{serial_number}-update', and supported_features equal to INSTALL | PROGRESS (integer value 5).

*   The installed_version property of the update entity must return the value from the 'info' service's 'version' property via the federwiege API.

*   The update entity must implement an async_update method that calls check_firmware_update() on the federwiege object. If the method returns a tuple (version_string, release_notes), the entity must set latest_version to the version string and release_summary to the release notes. If the method returns None, both latest_version and release_summary must be set to None, resulting in the entity state becoming unknown.

*   When installed_version equals latest_version, the update entity state must be 'off' (no update available). When latest_version is a newer version than installed_version, the state must be 'on' (update available).

*   The install action on the update entity must call .set(1) on the 'firmware_update' property from the 'system' service of the federwiege object.

*   The in_progress attribute must return True when the 'firmware_update_status' property from the 'system' service contains a status of DOWNLOADING or INSTALLING, and False when the status is IDLE, FAILED, or None.

*   The update entity must register a listener on the 'firmware_update_status' property when added to Home Assistant, and remove it when removed from Home Assistant, enabling real-time in_progress state updates.

*   The SmarlaBaseEntity base class in homeassistant/components/smarla/entity.py must store the federwiege object as self._federwiege in its __init__ method so it is accessible to subclasses like the update entity.


*   Interface details: Type: File Modification
Name: PLATFORMS in const.py
Location: homeassistant/components/smarla/const.py
Description: The PLATFORMS list must be updated to include Platform.UPDATE alongside the existing Platform.NUMBER, Platform.SENSOR, and Platform.SWITCH entries.

Type: File Modification
Name: SmarlaBaseEntity.__init__
Location: homeassistant/components/smarla/entity.py
Description: The SmarlaBaseEntity.__init__ method must store the federwiege object as self._federwiege so subclasses can access it. Signature: __init__(self, federwiege: Federwiege, desc: SmarlaEntityDescription) -> None

Type: Function
Name: async_setup_entry
Location: homeassistant/components/smarla/update.py
Signature: async_setup_entry(hass: HomeAssistant, config_entry: FederwiegeConfigEntry, async_add_entities: AddConfigEntryEntitiesCallback) -> None
Description: Sets up the Smarla firmware update entity from a config entry. Must call async_add_entities with a SmarlaUpdate instance.

Type: Class
Name: SmarlaUpdateEntityDescription
Location: homeassistant/components/smarla/update.py
Description: A frozen dataclass that combines SmarlaEntityDescription and UpdateEntityDescription. Used to describe the firmware update entity with key="update", service="info", property="version", and device_class=UpdateDeviceClass.FIRMWARE.

Type: Class
Name: SmarlaUpdate
Location: homeassistant/components/smarla/update.py
Description: Update entity class for the Smarla integration. Must extend both SmarlaBaseEntity and UpdateEntity.
Signature:
  __init__(self, federwiege: Federwiege, desc: SmarlaUpdateEntityDescription) -> None
  async async_update(self) -> None  — calls self._federwiege.check_firmware_update(); if None returned, sets _attr_latest_version and _attr_release_summary to None; if tuple returned, unpacks (target, notes) and sets _attr_latest_version=target and _attr_release_summary=notes
  async async_added_to_hass(self) -> None  — calls await self._update_status_property.add_listener(self.on_change)
  async async_will_remove_from_hass(self) -> None  — calls await self._update_status_property.remove_listener(self.on_change)
  in_progress (property) -> bool | None  — returns True when firmware_update_status is DOWNLOADING or INSTALLING; False when IDLE, FAILED, or None
  installed_version (property) -> str | None  — returns self._property.get()
  install(self, version: str | None, backup: bool, **kwargs: Any) -> None  — calls self._update_property.set(1)
  _attr_supported_features: UpdateEntityFeature.INSTALL | UpdateEntityFeature.PROGRESS  (integer value 5)
  _attr_should_poll: True
  _update_property: Property obtained via federwiege.get_property("system", "firmware_update")
  _update_status_property: Property obtained via federwiege.get_property("system", "firmware_update_status")


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.