Implement a document duplication feature in Payload CMS that correctly handles localized content with nested structures. Ensure that all locale-specific data is preserved and new IDs are generated for all nested records during duplication.

*   Ensure recursive ID generation:
    *   Implement recursive ID generation for all nested block and array records when duplicating documents with localized blocks containing nested arrays.
    *   Ensure that new unique database IDs are assigned to every level of nested blocks and arrays.

*   Preserve locale-specific data:
    *   Ensure that duplicating documents with localized groups inside tab fields retains all per-locale data, including nested array fields within the group.
    *   Verify that all locale-specific field values in localized groups inside tabs match the original document exactly, preserving text fields and each element of nested arrays per locale.

*   Update collection schema:
    *   Ensure the collection schema for the test collection (slug: withRequiredLocalizedFields) includes:
        *   A `nestedArray` field (type: array, with a text subfield) inside the text block type used in the layout blocks field.
        *   A `tabs` field containing a named tab (`name: myTab`) with a text field and a localized group field (`name: group`, localized: true) that contains:
            *   A `nestedArray2` array field (with a `nestedText` subfield).
            *   A `nestedText` field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.