Implement utility functions for JSON schema manipulation in the dashboard's JSON schema utilities module. These functions should convert values to schemas, verify value-schema matches, and extract constants from schemas, ensuring a round-trip guarantee.

*   Implement `constantValueToSchema` function:
    *   Accept any JavaScript value.
    *   Return a JSON schema object representing the constant value or null/undefined if no schema can represent it.
    *   Ensure that if a non-null schema is returned, `constantValue` with an empty definitions object and that schema returns an array with the original value as the first element.
    *   Ensure that if a non-null schema is returned, `isMatch` with an empty definitions object, that schema, and the original value returns true.

*   Implement `isMatch` function:
    *   Accept a definitions object, a schema, and a value.
    *   Return true if the value matches the schema's constraints, false otherwise.
    *   Handle type constraints for 'string', 'number', and 'integer'.
    *   Return true for string values against a schema with type 'string'.
    *   Return true for a string value against a const schema `{ const: value, type: 'string' }` if the value matches the const.
    *   Return true for finite floats against a schema with type 'number'.
    *   Return true for finite numbers against a const schema `{ const: value, type: 'number' }` if the value matches the const.
    *   Return true for 0, N itself, and exact integer multiples N*M against a schema `{ type: 'number', multipleOf: N }` or `{ type: 'integer', multipleOf: N }` where N is finite.
    *   Return false for non-integer multiples such as N*(M + 0.5) against a multipleOf schema with N ≠ 0, within `Number.MAX_SAFE_INTEGER`.
    *   Return true for integer values against a schema with type 'integer'.
    *   Return true for an integer value against a const schema `{ const: value, type: 'integer' }` if the value matches the const.

*   Implement `constantValue` function:
    *   Accept a definitions object and a schema.
    *   Return an array with the first element as the constant value from a const schema like `{ const: value, type: 'string' }`, `{ const: value, type: 'number' }`, or `{ const: value, type: 'integer' }`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.