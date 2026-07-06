I'm having an issue with dirty field tracking in my forms that use dynamic field lists.

*   When an item is appended to a field array, the dirtyFields object must only contain a key for that specific array (with each appended item's sub-fields marked true). Fields belonging to other unrelated scalar inputs or other field arrays that were not touched must NOT appear in dirtyFields.

*   When an item is removed from a field array, the dirtyFields object must only contain a key for that specific array. Unrelated scalar fields that the user did not modify must NOT appear in dirtyFields.

*   When a user directly modifies a scalar input field AND separately appends to a field array, both the modified scalar field (marked true) and the array field must appear in dirtyFields. Fields not touched by the user must still not appear.

*   When multiple independent field arrays exist in the same form, appending to one array must not cause the other array's key to appear in dirtyFields. Each array's dirty state is independently tracked.

*   When a field array uses a nested dot-notation path (e.g., a parent object containing the array), the root-level key of that parent object must appear in dirtyFields after an append or remove, but other unrelated root-level fields must not appear.

*   For indexed nested field arrays (e.g., a field array nested at a specific index of another field array), appending a new item must produce correct dirty state in the parent array branch: existing default items have their sub-fields marked false, and newly appended items have their sub-fields marked true.

*   After an indexed nested field array is restored to its default state (e.g., by removing a previously appended item), the root-level key for that nested array must no longer appear in dirtyFields.

*   Multiple successive appends to the same field array must accumulate correctly: the array's entry in dirtyFields must have a length equal to the number of appended items, with each item's sub-fields marked true.

*   Dirty state from a user-modified scalar field must be preserved across subsequent field array append and remove operations — those operations must not erase or reset the scalar field's dirty state.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.