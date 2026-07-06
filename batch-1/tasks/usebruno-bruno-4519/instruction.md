Implement the `interpolate` function to enhance Bruno's variable interpolation system. Ensure it correctly handles complex data types and supports both bracket-notation and dot-notation for template placeholders.

Requirements:

*   Implement the `interpolate` function with the signature `interpolate(str: string, obj: object, options?: object) -> string`.
*   Support bracket-notation syntax within template placeholders:
    *   Use `{{parent['key.with.dot']}}` to access properties with dots or special characters as literal keys.
    *   Perform exact key lookups with bracket notation, without traversing nested properties.
*   Maintain dot-notation functionality for traversing nested object properties (e.g., `{{data.user.name}}`).
*   Allow both bracket and dot notations to coexist in the same template string, resolving independently.
*   Serialize placeholders resolving to complex types:
    *   Convert plain objects to compact JSON strings using `JSON.stringify`.
    *   Convert arrays to compact JSON array strings using `JSON.stringify`.
    *   Substitute 'null' for placeholders resolving to null.
    *   Serialize Date objects to quoted ISO-8601 strings using `JSON.stringify`.
    *   Serialize moment.js objects to quoted ISO-8601 strings using `JSON.stringify`.
*   Ensure entire objects containing Date or moment.js properties are serialized with dates/moments in quoted ISO-8601 form.
*   Resolve nested template placeholders within object values before serializing the object to JSON.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.