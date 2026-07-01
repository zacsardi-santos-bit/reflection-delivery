Implement support for IAM permissions groups in the Terraform provider for a cloud platform. Create a managed resource for permissions groups, and add data sources for querying single and multiple groups. Ensure all components are registered correctly in the provider to pass schema validation.

*   Define a Go struct `IamPermissionsGroup` in `ovh/types_iam.go` with:
    *   Exported fields: Name (string), Id (string), Urn (string), Owner (string), Description (string), Permissions (IamPermissions).
    *   Implement a method `ToMap() map[string]any` returning a map with keys: name, owner, created_at, urn; optionally allow, except, deny (as []string slices); and optionally description and updated_at when non-empty.

*   Implement and register a Terraform resource `ovh_iam_permissions_group`:
    *   Define in `ovh/resource_iam_permission_group.go` with function signature `resourceIamPermissionsGroup() *schema.Resource`.
    *   Expose schema attributes: name (required string), description (required string), allow (optional TypeSet of strings), except (optional TypeSet of strings), deny (optional TypeSet of strings), urn (computed string).
    *   Support import by URN.
    *   Perform CRUD operations using OVH v2 IAM API:
        *   POST to `/v2/iam/permissionsGroup` to create.
        *   GET to `/v2/iam/permissionsGroup/{urn}` to read.
        *   PUT to `/v2/iam/permissionsGroup/{urn}` to update.
        *   DELETE to `/v2/iam/permissionsGroup/{urn}` to delete.
        *   Ensure URN in the path is URL-encoded.

*   Implement and register a Terraform data source `ovh_iam_permissions_group` (singular):
    *   Define in `ovh/data_iam_permissions_group.go` with function signature `dataSourceIamPermissionsGroup() *schema.Resource`.
    *   Accept `urn` as a required string input.
    *   Return attributes: name, description, allow, except, deny.
    *   Read from GET `/v2/iam/permissionsGroup/{urn}` (URL-encoded URN).

*   Implement and register a Terraform data source `ovh_iam_permissions_groups` (plural):
    *   Define in `ovh/data_iam_permissions_groups.go` with function signature `dataSourceIamPermissionsGroups() *schema.Resource`.
    *   Expose a computed attribute `urns` (TypeSet of strings) populated from the Urn field of each entry returned by GET `/v2/iam/permissionsGroup`.
    *   Ensure URN of any previously created `ovh_iam_permissions_group` resource appears in this set.

*   Register all new identifiers in `ovh/provider.go`:
    *   Add `ovh_iam_permissions_group` to the ResourcesMap.
    *   Add `ovh_iam_permissions_group` and `ovh_iam_permissions_groups` to the DataSourcesMap.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.