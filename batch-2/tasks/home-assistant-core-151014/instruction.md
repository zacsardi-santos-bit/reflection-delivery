I'm working with the Schlage lock integration in Home Assistant and I'd like to be able to manage PIN access codes from within Home Assistant automations.

*   Must add three string constants to homeassistant/components/schlage/const.py: SERVICE_ADD_CODE = "add_code", SERVICE_DELETE_CODE = "delete_code", SERVICE_GET_CODES = "get_codes".

*   Must implement an async_setup function in homeassistant/components/schlage/__init__.py that registers all three services as platform entity services for the lock domain. The add_code service schema must enforce that the 'code' field matches the regex r"^\d{4,8}$" (4 to 8 numeric digits only), so non-numeric strings, strings shorter than 4 digits, and strings longer than 8 digits are all rejected at the schema validation level.

*   The add_code method on SchlageLockEntity must first call refresh_access_codes on the underlying lock object. If that call raises a SchlageError, it must raise HomeAssistantError with translation_key="schlage_refresh_failed". After a successful refresh, it must call add_access_code with an AccessCode object constructed from the provided name and code. If add_access_code raises SchlageError, it must raise HomeAssistantError with translation_key="schlage_add_code_failed".

*   The delete_code method on SchlageLockEntity must perform case-insensitive name matching when searching for the code to delete. If access_codes is None, empty, or no code with the given name exists, the method must return silently without raising any error. If the delete call on the matching code object raises SchlageError, it must raise HomeAssistantError with translation_key="schlage_delete_code_failed".

*   The get_codes method on SchlageLockEntity must refresh access codes first (raising HomeAssistantError with translation_key="schlage_refresh_failed" on SchlageError). It must return a dict keyed by code ID, where each entry contains "name" and "code" string fields. When access_codes is None or an empty dict, it must return an empty dict {}.

*   Must add error translation keys to homeassistant/components/schlage/strings.json under "exceptions": "schlage_refresh_failed", "schlage_add_code_failed", "schlage_delete_code_failed", "schlage_name_exists" (with a {name} placeholder), and "schlage_code_exists".

*   The get_codes service must be registered with supports_response=SupportsResponse.ONLY so that callers can retrieve its return value.

*   When delete_code is called for a name that exists among other codes but does not match any code, the delete method must not be invoked on any existing code object.


*   Interface details: Type: Constant
Name: SERVICE_ADD_CODE
Location: homeassistant/components/schlage/const.py
Signature: SERVICE_ADD_CODE = "add_code"
Description: String constant identifying the add_code service name. Must equal the string "add_code".

Type: Constant
Name: SERVICE_DELETE_CODE
Location: homeassistant/components/schlage/const.py
Signature: SERVICE_DELETE_CODE = "delete_code"
Description: String constant identifying the delete_code service name. Must equal the string "delete_code".

Type: Constant
Name: SERVICE_GET_CODES
Location: homeassistant/components/schlage/const.py
Signature: SERVICE_GET_CODES = "get_codes"
Description: String constant identifying the get_codes service name. Must equal the string "get_codes".

Type: Function
Name: async_setup
Location: homeassistant/components/schlage/__init__.py
Signature: async_setup(hass: HomeAssistant, config: ConfigType) -> bool
Description: Sets up the Schlage integration and registers the three services (add_code, delete_code, get_codes) as platform entity services for the lock domain using service.async_register_platform_entity_service. The add_code service schema must validate that "code" matches the regex r"^\d{4,8}$" (4 to 8 numeric digits only). The get_codes service must be registered with supports_response=SupportsResponse.ONLY.

Type: Method
Name: add_code
Location: homeassistant/components/schlage/lock.py
Signature: async def add_code(self, name: str, code: str) -> None
Description: Method on SchlageLockEntity. Refreshes access codes from the lock first (raises HomeAssistantError with translation_key="schlage_refresh_failed" on SchlageError). Then calls the lock's add_access_code with an AccessCode(name=name, code=code) object. Raises HomeAssistantError with translation_key="schlage_add_code_failed" if the add operation fails with SchlageError.

Type: Method
Name: delete_code
Location: homeassistant/components/schlage/lock.py
Signature: async def delete_code(self, name: str) -> None
Description: Method on SchlageLockEntity. Refreshes access codes then deletes the code matching the given name using case-insensitive comparison. Returns silently (no error) when access_codes is None, empty, or the named code is not found. Raises HomeAssistantError with translation_key="schlage_delete_code_failed" if the delete call raises SchlageError.

Type: Method
Name: get_codes
Location: homeassistant/components/schlage/lock.py
Signature: async def get_codes(self) -> ServiceResponse
Description: Method on SchlageLockEntity. Refreshes access codes from the lock (raises HomeAssistantError with translation_key="schlage_refresh_failed" on SchlageError). Returns a dict keyed by code ID where each value is {"name": str, "code": str}. Returns {} when access_codes is None or empty.

Type: Translation keys
Name: Error translation keys
Location: homeassistant/components/schlage/strings.json
Description: The following keys must be defined under "exceptions" in strings.json for the Schlage domain:
- "schlage_refresh_failed": raised when refresh_access_codes fails (used by both add_code and get_codes)
- "schlage_add_code_failed": raised when add_access_code fails
- "schlage_delete_code_failed": raised when delete fails on an access code object
- "schlage_name_exists": raised when a duplicate code name is detected (translation_placeholders must include {"name": name})
- "schlage_code_exists": raised when a duplicate code value is detected


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.