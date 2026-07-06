I'm working on the vacuum integration and I've noticed that when you call the clean-area service with area IDs that don't have any segment mappings configured, the service just silently returns without doing anything or raising an error.

*   When the SERVICE_CLEAN_AREA service is called targeting multiple vacuum entities with a list of area IDs, and one or more requested areas cannot be mapped to vacuum segments by any of the targeted entities, a ServiceValidationError must be raised with translation_key equal to 'areas_not_mapped' and translation_placeholders containing an 'areas' key whose value is a string of the unmapped area IDs.

*   When some requested areas have valid segment mappings and some do not, the service must still invoke cleaning for each entity's mapped segments before raising the ServiceValidationError — entities with mappings for requested areas must receive a clean call with those segments even when the overall service call results in an error.

*   The StateVacuumEntity.async_internal_clean_area method must accept two arguments: a list of StateVacuumEntity instances and a ServiceCall object (rather than being called as an instance method with just a list of area IDs). The cleaning area IDs are read from the ServiceCall data.

*   If any entity in the list passed to StateVacuumEntity.async_internal_clean_area does not have its registry entry set, a RuntimeError must be raised with the message 'Cannot perform area clean, registry entry is not set'.


*   Interface details: Type: Method
Name: async_internal_clean_area
Class: StateVacuumEntity
Location: homeassistant/components/vacuum/__init__.py
Signature: async_internal_clean_area(entities: list[StateVacuumEntity], call: ServiceCall) -> None
Description: Class-level (static) async method that handles the clean-area service for a list of vacuum entities. Takes a list of StateVacuumEntity instances and a ServiceCall whose data contains "cleaning_area_id" (a list of area ID strings). For each entity, maps the requested area IDs to vacuum segment IDs using the entity's registry options, and invokes cleaning for any matched segments. Raises RuntimeError with the message "Cannot perform area clean, registry entry is not set" if any entity has no registry entry. After processing all entities, raises ServiceValidationError with translation_key="areas_not_mapped" and translation_placeholders={"areas": <string of unmapped area IDs>} if any requested areas could not be mapped to segments by any entity. Previously this was an instance method with signature async_internal_clean_area(self, areas: list[str]).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.