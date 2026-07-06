I'm running into a bug with field array validation.

*   When a resolver returns both a root-level error on a field array (an object with type and message properties) and nested per-item errors (indexed entries with field-level errors) for the same array field simultaneously, the form state must store and expose both types of errors at the same time — root error accessible via errors.fieldName.type and errors.fieldName.message, and item errors accessible via errors.fieldName[index].fieldProperty.

*   After an item is removed from a field array and validation is re-triggered, root-level array errors (those with type and message on the array itself) must remain visible in the form error state.

*   After an item is removed from a field array and validation is re-triggered, nested per-item field errors must be re-indexed to reflect the updated item positions — an item that shifts from index N to index M due to a removal must have its errors appear at the new index M.

*   When both root-level and item-level errors coexist for the same field array, performing a remove operation followed by re-validation must not cause existing item-level errors to be silently dropped — all errors that the resolver returns must be reflected in the form error state.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.