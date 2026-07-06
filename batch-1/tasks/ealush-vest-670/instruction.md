Implement a static method `enforce.context()` to provide custom validation rules with access to their execution context within a validation schema. This context should include the current value, positional metadata, and a mechanism to traverse up to parent scopes.

Requirements:
*   Implement `enforce.context()` as a static method on the `enforce` object.
    *   Ensure it is callable within custom rule implementations added via `enforce.extend()`.
*   `enforce.context()` must return an object with:
    *   `value`: the currently validated value.
    *   `meta`: an object with contextual metadata.
    *   `parent`: a function returning the parent context or `null`.
*   At the top level (e.g., `enforce(value).someRule()`):
    *   `value` must equal the validated value.
    *   `meta` must be an empty object `{}`.
    *   `parent()` must return `null`.
*   For custom rules inside a shape or loose schema validation:
    *   `meta` must be `{ key: '<fieldName>' }`, where `fieldName` is the key being validated.
*   For custom rules inside an `isArrayOf` validation:
    *   `meta` must be `{ index: N }`, where `N` is the zero-based index of the current array element.
*   The `parent()` function must:
    *   Return the context object of the enclosing validation scope.
    *   Return `null` if there is no enclosing scope.
    *   Support chaining to traverse multiple levels up the scope tree, terminating with `null`.
*   Within nested schema rules, the context object's `value` must be the value of the specific field or array element being validated.
*   For shape or loose validation, the parent context's `value` must equal the object being schema-validated at that level.
*   Enable custom rules using `enforce.context()` to traverse the parent chain for cross-field validation, such as comparing an array element against a field on the grandparent object.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.