Implement support for associating automanage configuration profiles with Azure Stack HCI clusters in the Terraform provider. Add parsing and validation utilities to handle the specific resource ID format for these assignments. Ensure the Terraform resource can manage these configurations declaratively.

*   Update the `AutomanageConfigurationHCIAssignmentId` struct:
    *   Include fields: `SubscriptionId`, `ResourceGroup`, `ClusterName`, `ConfigurationProfileAssignmentName`.
    *   Implement the `resourceids.Id` interface.
    *   Ensure the `ID()` method returns the canonical resource ID string format: `/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.AzureStackHci/clusters/{ClusterName}/providers/Microsoft.Automanage/configurationProfileAssignments/{ConfigurationProfileAssignmentName}`.

*   Implement `NewAutomanageConfigurationHCIAssignmentID` function:
    *   Accept parameters: `subscriptionId`, `resourceGroup`, `clusterName`, `configurationProfileAssignmentName`.
    *   Return an `AutomanageConfigurationHCIAssignmentId` with the correct ID format.

*   Develop `AutomanageConfigurationHCIAssignmentID` parsing function:
    *   Return an error for invalid inputs: empty strings, bare slashes, missing segments, or upper-cased paths.
    *   Return a populated `*AutomanageConfigurationHCIAssignmentId` for valid, fully-lowercase canonical IDs.

*   Create `AutomanageConfigurationHCIAssignmentID` validation function:
    *   Return errors for invalid inputs: empty strings, missing segments, incomplete paths, or upper-cased paths.
    *   Return no errors for valid, fully-formed, lowercase IDs.

*   Update `azurerm_stack_hci_cluster` Terraform resource:
    *   Add optional attribute `automanage_configuration_id` to accept the resource ID of an automanage configuration profile assignment.
    *   Allow setting `automanage_configuration_id` during create/update operations.
    *   Ensure the attribute can be removed (unset) without errors in subsequent applies.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.