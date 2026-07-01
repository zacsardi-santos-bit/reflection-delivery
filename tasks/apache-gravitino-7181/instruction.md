Implement a new cache key class, `EntityCacheKey`, to represent entries in an entity cache, distinguishing between directly stored entities and relational associations. Ensure the class is immutable and provides a clear string representation for debugging purposes.

*   Implement the `EntityCacheKey` class in the `org.apache.gravitino.cache` package at the path `core/src/main/java/org/apache/gravitino/cache/EntityCacheKey.java`.
*   Provide two static factory methods named `of`:
    *   A three-argument method accepting `NameIdentifier`, `Entity.EntityType`, and `SupportsRelationOperations.Type` (nullable).
    *   A two-argument method accepting `NameIdentifier` and `Entity.EntityType`, equivalent to calling the three-argument form with a null `relationType`.
*   Ensure the three-argument factory method throws `IllegalArgumentException` if `NameIdentifier` or `Entity.EntityType` is null. Allow null for `relationType`.
*   Implement accessor methods:
    *   `NameIdentifier identifier()`
    *   `Entity.EntityType entityType()`
    *   `SupportsRelationOperations.Type relationType()`
*   Define the `toString()` method to return a colon-separated string:
    *   Format without `relationType`: `<identifier-string>:<entityType-shortName>`.
    *   Format with `relationType`: `<identifier-string>:<entityType-shortName>:<relationType-name>`.
    *   Use `Entity.EntityType.getShortName()` for `entityType-shortName`.
    *   Use the enum constant name for `relationType-name`.
*   Implement `equals()` to return true if two `EntityCacheKey` instances have identical `identifier`, `entityType`, and `relationType` (including both null).
*   Implement `hashCode()` to be consistent with `equals()`, incorporating all three fields: `identifier`, `entityType`, and `relationType`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.