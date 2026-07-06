Implement fixes in the fake Kubernetes client to address metadata handling issues with typed Go objects and pointer-embedded metadata. Ensure that the client behaves consistently with real Kubernetes clients for all standard operations.

*   Ensure the fake client's Get method:
    *   Leaves the TypeMeta field as the zero value (empty struct) for typed objects, without populating API version and kind fields.

*   Ensure the fake client's List method:
    *   Leaves the ListMeta field on the list and the TypeMeta field on each item as zero values, without populating them.

*   Support operations on objects with pointer-embedded metadata:
    *   Allow Get operations to succeed without error for objects where TypeMeta and ObjectMeta are embedded as pointers.
    *   Allow List operations to return the correct number of elements in the items slice when objects exist, or an empty slice when no objects exist.
    *   Allow Patch operations to persist label changes, which must be visible on a subsequent Get.
    *   Allow Update operations to persist label changes, which must be visible on a subsequent Get.
    *   Ensure Delete operations result in a not-found error on a subsequent Get for the same object.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.