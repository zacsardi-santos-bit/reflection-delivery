Fix the equality implementation for legacy wrapper types and handle dictionary key conflicts during migration in the Cadence storage migration system.

*   Implement equality methods for legacy wrapper types:
    *   LegacyCharacterValue: Implement `Equal(inter *interpreter.Interpreter, locationRange interpreter.LocationRange, other interpreter.Value) bool` in `migrations/legacy_character_value.go`. Ensure it unwraps `other` if it is a *LegacyCharacterValue before comparing underlying values.
    *   LegacyStringValue: Implement `Equal(inter *interpreter.Interpreter, locationRange interpreter.LocationRange, other interpreter.Value) bool` in `migrations/legacy_string_value.go`. Ensure it unwraps `other` if it is a *LegacyStringValue before comparing underlying values.
    *   LegacyIntersectionType: Implement `Equal(other interpreter.StaticType) bool` in `migrations/legacy_intersection_type.go`. Ensure it unwraps `other` if it is a *LegacyIntersectionType before comparing underlying types.
    *   LegacyPrimitiveStaticType: Implement `Equal(other interpreter.StaticType) bool` in `migrations/legacy_primitivestatic_type.go`. Ensure it unwraps `other` if it is a LegacyPrimitiveStaticType before comparing underlying types.
    *   LegacyReferenceType: Implement `Equal(other interpreter.StaticType) bool` in `migrations/legacy_reference_type.go`. Ensure it unwraps `other` if it is a *LegacyReferenceType before comparing underlying types.

*   Handle dictionary key conflicts during migration:
    *   In `migrations/migration.go`, within `MigrateNestedValue`, after removing an old dictionary key and computing the new key, check if the new key already exists in the dictionary.
    *   If the new key exists, panic with the error message: "dictionary contains new key after removal of old key (conflict)".
    *   Capture this panic as a `StorageMigrationError` in the migration reporter, including `StorageKey`, `StorageMapKey`, and `Stack` fields.
    *   Ensure that exactly one migration entry is recorded in the migration reporter's migrated list for the first successfully migrated key.
    *   Ensure subsequent storage health checks report unreferenced slabs due to orphaned data.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.