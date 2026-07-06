I'm working with the Sure Petcare integration in Home Assistant and I'd like to expose some additional pet position data as sensor entities.

*   Two new sensor entities must be created for each pet entity in the surepetcare integration: one that reports the ID of the last flap device the pet was detected at, and one that reports the ID of the user who last manually recorded the pet's location.

*   The last-seen flap device sensor must have a unique ID following the pattern '{household_id}-{pet_id}-last_seen_flap_device' and must produce a state equal to the string representation of the 'device_id' field from the pet's position data.

*   The last-seen user sensor must have a unique ID following the pattern '{household_id}-{pet_id}-last_seen_user' and must produce a state equal to the string representation of the 'user_id' field from the pet's position data.

*   Both new pet sensors must be disabled by default via the integration (RegistryEntryDisabler.INTEGRATION). When disabled, the entities must be present in the entity registry but must not have an active state.

*   The pet position data provided by the API (and used in tests) must include 'device_id' and 'user_id' fields in addition to the existing 'since' and 'where' fields.

*   The sensor platform setup must create PetLastSeenFlapDevice and PetLastSeenUser sensor entities for every entity of type PET in the coordinator data.

*   PetLastSeenFlapDevice must set _attr_entity_registry_enabled_default = False and compute its unique ID as '{device_id}-last_seen_flap_device' in __init__.

*   PetLastSeenUser must set _attr_entity_registry_enabled_default = False and compute its unique ID as '{device_id}-last_seen_user' in __init__.


*   Interface details: Type: Class
Name: PetLastSeenFlapDevice
Location: homeassistant/components/surepetcare/sensor.py
Description: Sensor entity that reports the ID of the last flap device a pet was detected at. Must set _attr_entity_registry_enabled_default = False to be disabled by default. Must set unique ID to '{device_id}-last_seen_flap_device' in __init__. State is the string representation of the 'device_id' field from the pet's position data (None/unknown if not present). Must inherit from SurePetcareEntity and SensorEntity.
Signature: __init__(self, surepetcare_id: int, coordinator: SurePetcareDataCoordinator) -> None

Type: Class
Name: PetLastSeenUser
Location: homeassistant/components/surepetcare/sensor.py
Description: Sensor entity that reports the ID of the user who last manually recorded the pet's location. Must set _attr_entity_registry_enabled_default = False to be disabled by default. Must set unique ID to '{device_id}-last_seen_user' in __init__. State is the string representation of the 'user_id' field from the pet's position data (None/unknown if not present). Must inherit from SurePetcareEntity and SensorEntity.
Signature: __init__(self, surepetcare_id: int, coordinator: SurePetcareDataCoordinator) -> None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.