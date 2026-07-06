Implement functionality in the Wasmer C API to allow inspection of a WebAssembly module's import requirements before instantiation. Provide a way to retrieve, iterate, and release a collection of import descriptors, detailing each import's kind, name, and module/namespace.

*   Define new opaque types in `wasmer.h` and `wasmer.hh`:
    *   `wasmer_import_descriptor_t`: Represents a single import descriptor.
    *   `wasmer_import_descriptors_t`: Represents a collection of import descriptors.

*   Implement the `wasmer_import_descriptors` function:
    *   Accepts a `wasmer_module_t` pointer and a pointer-to-pointer for `wasmer_import_descriptors_t`.
    *   Populates the output pointer with a heap-allocated collection of all imports from the module.
    *   The caller owns the result and must free it using `wasmer_import_descriptors_destroy`.

*   Implement the `wasmer_import_descriptors_len` function:
    *   Accepts a `wasmer_import_descriptors_t` pointer.
    *   Returns the number of import descriptors as a C `int`.

*   Implement the `wasmer_import_descriptors_get` function:
    *   Accepts a `wasmer_import_descriptors_t` pointer and a zero-based integer index.
    *   Returns a pointer to the `wasmer_import_descriptor_t` at that index.

*   Implement the `wasmer_import_descriptor_kind` function:
    *   Accepts a `wasmer_import_descriptor_t` pointer.
    *   Returns the kind of the import as a `wasmer_import_export_kind` value.

*   Implement the `wasmer_import_descriptor_name` function:
    *   Accepts a `wasmer_import_descriptor_t` pointer.
    *   Returns a `wasmer_byte_array` with the import's name and its length.

*   Implement the `wasmer_import_descriptor_module_name` function:
    *   Accepts a `wasmer_import_descriptor_t` pointer.
    *   Returns a `wasmer_byte_array` with the import's module/namespace name and its length.

*   Implement the `wasmer_import_descriptors_destroy` function:
    *   Accepts a `wasmer_import_descriptors_t` pointer.
    *   Frees all memory associated with the collection.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.