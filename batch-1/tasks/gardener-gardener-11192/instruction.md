Implement support for garden-level extensions in the Garden resource by adding an extensions list field. Extend the validation logic to reject duplicate extension types and add lifecycle operations for deleting and waiting for the cleanup of all extension resources.

*   Update the GardenSpec struct:
    *   Add an `Extensions` field of type `[]GardenExtension` in `pkg/apis/operator/v1alpha1/types_garden.go`.
    *   Define `GardenExtension` struct with:
        *   `Type` string field.
        *   `ProviderConfig` field as a pointer to `runtime.RawExtension`.

*   Extend the ValidateGarden function in `pkg/apis/operator/v1alpha1/validation/validation.go`:
    *   Validate the `spec.extensions` list for duplicate `Type` values.
    *   Return a `field.Error` of type `field.ErrorTypeDuplicate` at `spec.extensions[N].type` for duplicates, where N is the index of the duplicate.
    *   Ensure no errors are returned for valid extensions with distinct types.

*   Modify the extension component's Interface in `pkg/component/extensions/extension/extension.go`:
    *   Add `DeleteResources(ctx context.Context) error` method:
        *   Delete all extension resources in the target namespace.
        *   Ensure listing extension resources afterward returns an empty list.
    *   Add `WaitCleanupResources(ctx context.Context) error` method:
        *   Return `nil` when no extension resources exist.
        *   Return an error with the message containing "Extension <namespace>/<name> is still present" if resources remain.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.