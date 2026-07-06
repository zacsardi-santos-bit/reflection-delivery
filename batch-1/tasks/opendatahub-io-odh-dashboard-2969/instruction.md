Refactor the role binding permission management system to make it reusable across different features, such as model registries. Implement a new permissions management page for model registries, allowing platform administrators to manage access for users and groups.

*   Update the role binding generator:
    *   Implement `generateRoleBindingPermissions` in `frontend/src/api/k8s/roleBindings.ts`.
        *   Accept parameters: `namespace` (string), `rbSubjectKind` (RoleBindingPermissionsRBType), `rbSubjectName` (string), `rbRoleRefName` (RoleBindingPermissionsRoleType or string), `rbRoleRefKind` (string).
        *   Ensure metadata.name matches `/^dashboard-permissions-[a-zA-Z0-9]+$/`.
        *   When called with exactly five arguments, set metadata.labels to `{ [KnownLabels.DASHBOARD_RESOURCE]: 'true' }`.
        *   Ensure apiVersion, subjects, and roleRef match those from `createRoleBindingObject`.

*   Define shared types:
    *   Export `RoleBindingPermissionsRBType` from `frontend/src/concepts/roleBinding/types.ts` with members `USER` and `GROUP` as 'User' and 'Group'.
    *   Export `RoleBindingPermissionsRoleType` from `frontend/src/concepts/roleBinding/types.ts` with members `EDIT` and `ADMIN`.

*   Update UI components:
    *   Use `data-testid='role-binding-table User'` and `data-testid='role-binding-table Group'` for role binding tables.
    *   Use `data-testid='role-binding-name-input'` for the role binding name input field.

*   Implement the model registry permissions page:
    *   Accessible at `/modelRegistrySettings/permissions/{registryName}`.
    *   Restrict access to platform administrators; show `data-testid='not-found-page'` for non-admins.
    *   Redirect to `/modelRegistrySettings` if the registry does not exist.
    *   Display separate tables for users and groups, supporting sorting by Name and Date Added.

*   Manage role bindings on the permissions page:
    *   Adding a user: Create a RoleBinding via HTTP POST with subject kind='User' and roleRef name='registry-user-{registryName}'.
    *   Adding a group: Create a RoleBinding with subject kind='Group' and same roleRef name.
    *   Ensure metadata labels include `app='{registryName}'`, `'app.kubernetes.io/component'='model-registry'`, `'app.kubernetes.io/part-of'='model-registry'`, `'app.kubernetes.io/name'='{registryName}'`, `'opendatahub.io/dashboard'='true'`, `component='model-registry'`.
    *   Editing a user or group: Create a new RoleBinding and delete the old one.
    *   Deleting a user or group: Issue an HTTP DELETE for the RoleBinding.
    *   Disable action menu for system/default groups; enable for manually-added groups.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.