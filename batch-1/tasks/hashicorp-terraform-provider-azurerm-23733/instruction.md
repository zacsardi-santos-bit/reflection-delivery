Implement support for customer-managed key encryption for Azure NetApp Files in Terraform. Assign managed identities to NetApp accounts, configure encryption settings with Key Vault keys, and ensure volumes can specify encryption sources. Validate NetApp account resource IDs to prevent misconfigurations.

*   Implement the `ValidateNetAppAccountID` function in `internal/services/netapp/validate/account_id.go`:
    *   Accept a full Azure NetApp Account resource ID path.
    *   Return errors for empty strings, a bare slash, paths with empty segments, and upper-cased paths.
    *   Return no errors for fully specified, lower-cased paths.

*   Extend the `azurerm_netapp_account` resource in `internal/services/netapp/netapp_account_resource.go`:
    *   Support an `identity` block with:
        *   `identity.0.type`: "SystemAssigned" or "UserAssigned".
        *   `identity.0.tenant_id` and `identity.0.principal_id` as computed attributes.
        *   `identity_ids` list required when type is "UserAssigned".
    *   Allow in-place updates of the `identity` block without resource replacement.

*   Extend the `data.azurerm_netapp_account` data source in `internal/services/netapp/netapp_account_data_source.go`:
    *   Expose the `identity` block, including `identity.0.type`.

*   Implement the `azurerm_netapp_account_encryption` resource in `internal/services/netapp/netapp_account_encryption_resource.go`:
    *   Accept `netapp_account_id`, `system_assigned_identity_principal_id`, `user_assigned_identity_id`, and `encryption_key`.
    *   Allow read-back of `encryption_key` and in-place updates to a different Key Vault key URL.

*   Implement the `data.azurerm_netapp_account_encryption` data source in `internal/services/netapp/netapp_account_encryption_data_source.go`:
    *   Accept `netapp_account_id` and expose `system_assigned_identity_principal_id` and `encryption_key`.

*   Extend the `azurerm_netapp_volume` resource in `internal/services/netapp/netapp_volume_resource.go`:
    *   Support `snapshot_directory_visible` as a boolean attribute accepting true.
    *   Support `encryption_key_source` with values "Microsoft.KeyVault" and "Microsoft.NetApp".
    *   Support `key_vault_private_endpoint_id` when `encryption_key_source` is "Microsoft.KeyVault".

*   Extend the `data.azurerm_netapp_volume` data source in `internal/services/netapp/netapp_volume_data_source.go`:
    *   Expose `encryption_key_source` with "Microsoft.NetApp" for Microsoft-managed keys.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.