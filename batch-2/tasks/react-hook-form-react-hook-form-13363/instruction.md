I'm working with a form library and I've noticed that the dirty fields tracking includes entries for fields that haven't actually changed from their defaults.

*   The getDirtyFields function must omit fields whose current value matches their default value from the result object, rather than including them with a false value.

*   When comparing two identical form value objects, getDirtyFields must return an empty object {}.

*   For array fields, array positions where all fields match their defaults must be represented as undefined in the result array, not as an object with all fields set to false.

*   For arrays where every position would be undefined (all items match defaults), the parent key must be set to undefined rather than holding an array of false-valued objects.

*   Nested objects that would only contain non-dirty entries (all matching defaults) must be pruned entirely from the result — including nested arrays within nested objects.

*   The pruning behavior must be recursive: after removing non-dirty scalar fields, any parent containers (objects or arrays) that become empty must also be removed from the result.

*   When a mix of dirty and clean items exist in an array, clean positions must be undefined while dirty positions retain their dirty field objects with only the actually-changed fields.


*   Interface details: Type: Function
Name: getDirtyFields
Location: src/logic/getDirtyFields.ts
Signature: getDirtyFields(dirtyFieldsRef: Record<string, any>, values: Record<string, any>) -> Record<string, any>
Description: Compares two form value objects (current values and default values) and returns a sparse object containing only the fields that differ. Non-dirty fields (those matching their defaults) must be omitted entirely. For arrays, clean positions must be undefined. Nested empty containers must be pruned recursively.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.