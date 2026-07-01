Refactor the Kargo Project initialization process to separate namespace setup from secret management permission setup. Implement the necessary changes in both the reconciler and webhook components to handle these steps distinctly, ensuring accurate error reporting and proper permission configuration.

Requirements:

*   Update the reconciler:
    *   Modify `newReconciler` to accept a `ReconcilerConfig` parameter and store it in the `cfg` field.
    *   Ensure `ensureNamespaceFn`, `ensureSecretPermissionsFn`, and `createRoleBindingFn` are initialized to non-nil values.
    *   Add `cfg`, `ensureNamespaceFn`, `ensureSecretPermissionsFn`, and `createRoleBindingFn` fields to the `reconciler` struct.
    *   Implement `syncProject` to:
        *   Call `ensureNamespaceFn` first and return its status and error unchanged if it fails.
        *   Call `ensureSecretPermissionsFn` after `ensureNamespaceFn` succeeds, returning a wrapped error if it fails.
        *   Set `status.Phase` to `ProjectPhaseReady` on full success.
    *   Implement `ensureNamespace` to:
        *   Return `(kargoapi.ProjectStatus, error)` without setting `ProjectPhaseReady` on success.
        *   Set `status.Phase` to `ProjectPhaseInitializationFailed` only for unrecoverable namespace conflicts.
        *   Include specific error messages for namespace get, create, and update errors, maintaining `ProjectPhaseInitializing`.
    *   Implement `ensureSecretPermissions` to:
        *   Return nil if `createRoleBindingFn` returns an AlreadyExists error.
        *   Return an error containing "error creating role binding" for other errors.

*   Update the webhook:
    *   Modify `newWebhook` to accept a `WebhookConfig` parameter and store it in the `cfg` field.
    *   Ensure `ensureNamespaceFn`, `ensureSecretPermissionsFn`, and `createRoleBindingFn` are initialized to non-nil values.
    *   Add `cfg`, `ensureNamespaceFn`, `ensureSecretPermissionsFn`, and `createRoleBindingFn` fields to the `webhook` struct, removing `updateNamespaceFn`.
    *   Implement `ensureNamespace` to:
        *   Return HTTP 500 for non-not-found namespace retrieval errors.
        *   Return HTTP 409 for specific namespace ownership conflicts.
        *   Return nil on successful namespace creation or ownership match.
    *   Implement `ensureSecretPermissions` to:
        *   Return nil if `createRoleBindingFn` returns an AlreadyExists error.
        *   Return HTTP 500 for other errors.
    *   Update `ValidateCreate` to delegate namespace setup to `ensureNamespaceFn`.

*   Configuration:
    *   Ensure `ReconcilerConfig` and `WebhookConfig` include a `KargoNamespace` field populated from the `KARGO_NAMESPACE` environment variable.
    *   Implement `ReconcilerConfigFromEnv()` and `WebhookConfigFromEnv()` to load configurations from the environment.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.