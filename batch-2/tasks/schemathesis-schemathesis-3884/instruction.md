I'm working with a stateful API testing tool that automatically infers dependencies between API operations by analyzing field names in request bodies and responses.

*   The `infer_fk_target` function in `schemathesis/specs/openapi/stateful/dependencies/models.py` must be extended to recognize camelCase foreign key field names in addition to already-supported snake_case names.

*   When given a camelCase field name matching the pattern `{ResourceName}Id`, `{ResourceName}Uuid`, or `{ResourceName}Guid`, `infer_fk_target` must return a 3-tuple `(resource_name: str, id_field: str, is_array: bool)` where resource_name is the PascalCase resource prefix, id_field is the lowercased suffix ('id', 'uuid', or 'guid'), and is_array is False. Examples: 'locationId' → ('Location', 'id', False), 'userUuid' → ('User', 'uuid', False), 'customerGuid' → ('Customer', 'guid', False), 'orderId' → ('Order', 'id', False), 'warehouseId' → ('Warehouse', 'id', False).

*   When the camelCase field name is the plural form (`{ResourceName}Ids`, `{ResourceName}Uuids`, or `{ResourceName}Guids`), `infer_fk_target` must return is_array=True. Examples: 'locationIds' → ('Location', 'id', True), 'userUuids' → ('User', 'uuid', True), 'orderGuids' → ('Order', 'guid', True).

*   `infer_fk_target` must return None for fields that are plain identifier names with no resource prefix ('id', 'uuid', 'guid'), for short words ending in 'id' ('bid', 'fid', 'lid', 'eid'), for common English words ending in identifier-like suffixes ('paid', 'valid', 'applied', 'denied', 'void'), and for camelCase fields that do not end in an identifier suffix ('customerName', 'displayLabel').

*   Snake_case foreign key field names that already work must continue to work unchanged: 'customer_id' → ('Customer', 'id', False), 'user_uuid' → ('User', 'uuid', False), 'session_guid' → ('Session', 'guid', False), 'site_ids' → ('Site', 'id', True), 'user_uuids' → ('User', 'uuid', True).

*   Dependency graph nodes must include a `fk_fields` attribute — a list where each entry is a dict with keys `field_name` (str), `is_array` (bool), `target_field` (str), and `target_resource` (str). For example, when 'brandId' is recognized as a FK, an entry `{'field_name': 'brandId', 'is_array': false, 'target_field': 'id', 'target_resource': 'Brand'}` must appear in `fk_fields`.

*   When a schema contains a nested camelCase FK field (e.g., `locationId` inside a nested `shipping` object in a request body), the `analyze` function must produce an input slot with `parameter_name == 'shipping/locationId'`, `resource.name == 'Location'`, and `resource_field == 'id'`.


*   Interface details: Type: Function
Name: infer_fk_target
Location: src/schemathesis/specs/openapi/stateful/dependencies/models.py
Signature: infer_fk_target(field: str) -> tuple[str, str, bool] | None
Description: Infers the foreign key target from a field name. Returns a 3-tuple (resource_name, id_field, is_array) if the field name matches a FK naming pattern, or None if no FK is detected. Must support both camelCase (e.g., "locationId", "userUuid", "orderGuids") and snake_case (e.g., "customer_id", "user_uuid", "site_ids") naming conventions. The resource_name is in PascalCase (e.g., "Location", "User"), id_field is lowercased (e.g., "id", "uuid", "guid"), and is_array is True for plural forms.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.