I'm running into two bugs with dirty field tracking in my forms.

*   When setValue is called with an array of objects and shouldDirty set to true, formState.dirtyFields must reflect the dirty state at the individual property level within each array item (e.g., { data: [{ id: true, name: true }] }) rather than marking the entire array field as a flat boolean (e.g., { data: true }).

*   When a field's value is changed programmatically without marking it dirty (shouldDirty: false), and a subsequent field change causes a discrepancy between the form-level isDirty flag and the field-level dirtyFields tracking, the dirtyFields object must be recomputed from actual field values compared to their default values.

*   After recomputation triggered by a dirty state mismatch, any field whose current value differs from its default value must appear in formState.dirtyFields, even if that field was originally set via setValue with shouldDirty: false.

*   formState.isDirty must remain true when any field holds a value different from its default, regardless of whether that field was marked dirty at the time the value was set.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.