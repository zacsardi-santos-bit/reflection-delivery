Implement functionality to prevent the removal of image pull secrets from service accounts during updates, ensuring that externally-managed secrets are preserved. Modify the `mergeState` function to retain existing image pull secrets when the desired state lacks them.

*   Update the `mergeState` function in `pkg/controller/utils/component.go`:
    *   Ensure the function signature is `mergeState(desired client.Object, current runtime.Object) client.Object`.
    *   Preserve the `ImagePullSecrets` field on `ServiceAccount` objects:
        *   If the current `ServiceAccount` has one or more `ImagePullSecrets` and the desired state has none, copy the existing `ImagePullSecrets` into the desired state.

*   Ensure the `CreateOrUpdateOrDelete` handler operates correctly:
    *   It must complete without error when updating a `ServiceAccount` that already exists, even if the update payload omits the `Secrets` and `ImagePullSecrets` fields.
    *   After execution, verify that:
        *   The `ServiceAccount` retains all previously existing `Secrets` entries.
        *   The `ServiceAccount` retains all previously existing `ImagePullSecrets` entries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.