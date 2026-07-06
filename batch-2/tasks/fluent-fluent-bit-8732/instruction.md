Implement support for "referenced" data in string and byte variants in the CFL library. Update existing functions to accept a flag indicating whether to copy or reference data, and add new functions to handle length-delimited strings with this capability. Ensure comprehensive test coverage for array operations, including dynamic resizing and element management.

*   Update functions to support referenced data:
    *   Modify `cfl_variant_create_from_string_s` to accept a `referenced` parameter.
    *   Modify `cfl_variant_create_from_bytes` to accept a `referenced` parameter.
    *   Modify `cfl_kvlist_insert_bytes` to accept a `referenced` parameter.
    *   Modify `cfl_kvlist_insert_string_s` to accept a `referenced` parameter.
    *   Modify `cfl_kvlist_insert_bytes_s` to accept a `referenced` parameter.
    *   Modify `cfl_array_append_bytes` to accept a `referenced` parameter.

*   Add new functions:
    *   Implement `cfl_array_append_string_s` with signature: `int cfl_array_append_string_s(struct cfl_array *array, char *str, size_t str_len, int referenced)`. Ensure it returns 0 on success.

*   Ensure correct behavior of array operations:
    *   Implement `cfl_array_create` to accept a `size_t` slot count and return a non-NULL `struct cfl_array` pointer.
    *   Implement `cfl_array_destroy` to free all resources held by the array.
    *   Implement `cfl_array_resizable` to enable or disable dynamic resizing.
    *   Ensure arrays created with slot count 0 reject appends unless made resizable.
    *   Implement `cfl_array_append_string` to return 0 on success.
    *   Implement `cfl_array_append_reference`, `cfl_array_append_bool`, `cfl_array_append_int64`, `cfl_array_append_uint64`, `cfl_array_append_double`, `cfl_array_append_null`, `cfl_array_append_array`, `cfl_array_append_new_array`, and `cfl_array_append_kvlist` to return 0 on success.
    *   Implement `cfl_array_remove_by_index` and `cfl_array_remove_by_reference` to return 0 on success and ensure subsequent fetches return NULL.
    *   Implement `cfl_array_fetch_by_index` to return a `struct cfl_variant` pointer or NULL if the slot is empty or out of range.

*   Ensure comprehensive test coverage for:
    *   Creating arrays with zero or positive initial capacity.
    *   Toggling dynamic resize on and off.
    *   Appending all supported element types.
    *   Fetching by index and removing elements by index or reference.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.