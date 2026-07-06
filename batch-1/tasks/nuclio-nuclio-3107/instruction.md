Update the `PopulateFieldsFromValues` function in `pkg/common/helper.go` to ensure it only populates fields that are currently at their zero value, leaving already-populated fields untouched. This function should handle string, integer, and boolean field types.

*   Implement the `PopulateFieldsFromValues` function with the following signature:
    *   `PopulateFieldsFromValues[T string | bool | int](fieldsToValues map[*T]T)`
    *   Ensure the function accepts a map of pointers-to-fields and their corresponding values.
*   Ensure the function only sets a field's value when:
    *   The field is currently at its zero value.
    *   The provided value is non-zero.
*   For string fields:
    *   Do not overwrite if the field already has a non-empty value.
    *   Set the field to the provided value if it is empty and the provided value is non-empty.
*   For integer fields:
    *   Do not overwrite if the field already has a non-zero value.
    *   Set the field to the provided value if it is zero and the provided value is non-zero.
*   For boolean fields:
    *   Do not overwrite if the field already holds `true`.
    *   Set the field to `true` if it is `false` and the provided value is `true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.