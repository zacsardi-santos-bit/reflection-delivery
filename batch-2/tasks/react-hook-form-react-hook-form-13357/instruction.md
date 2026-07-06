I'm having an issue with dirty state tracking in my form.

*   When setValue is called on an array field with shouldDirty set to true, and the new value is an empty array, the form's isDirty state must be set to true.

*   When setValue is called on an array field (that has registered child fields) with shouldDirty set to true and an empty array value, the dirtyFields state must reflect the parent array field as dirty (e.g., { data: true }), not any individual child fields.

*   Setting an array field to an empty array via setValue with shouldDirty: true must correctly update dirty tracking even when child fields of that array were previously registered.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.