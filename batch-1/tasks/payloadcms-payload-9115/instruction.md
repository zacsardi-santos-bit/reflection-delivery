Implement the `sanitizeRelationshipIDs` function to correctly convert string relationship IDs to native MongoDB ID objects without altering the document structure. Ensure that only present fields are processed, and the document structure remains unchanged for absent fields.

*   Implement the `sanitizeRelationshipIDs` function in `packages/db-mongodb/src/utilities/sanitizeRelationshipIDs.ts` with the following signature:
    *   `sanitizeRelationshipIDs({ config, data, fields }: { config: SanitizedConfig, data: Record<string, any>, fields: Field[] }): Record<string, any>`
*   Traverse all relationship fields in the provided data using the field schema.
    *   Convert each string hex ID to a `Types.ObjectId` instance.
    *   Mutate the data in place and return the modified data.
*   Ensure that absent fields are skipped by:
    *   Passing `fillEmpty: false` to the `traverseFields` utility to avoid filling missing fields with default values.
*   Convert all string hex IDs to `Types.ObjectId` instances with the same underlying value.
    *   Ensure `toHexString()` of each `Types.ObjectId` equals the original string value.
*   Handle various relationship field configurations:
    *   Single-collection relationship fields (scalar value).
    *   hasMany single-collection relationship fields (array of values).
    *   Polymorphic relationship fields (object with `relationTo` and `value`).
    *   hasMany polymorphic fields (array of `{ relationTo, value }` objects).
*   Process localized relationship fields:
    *   Convert IDs within each locale where field data is keyed by locale code (e.g., `{ en: ..., es: ... }`).
*   Correctly traverse and convert relationship IDs nested within:
    *   Array fields, including arrays of arrays.
    *   Block fields, identified by `blockType`.
    *   Group fields.
    *   Row fields.
    *   Tab fields.
    *   All combinations including localized variants of these container fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.