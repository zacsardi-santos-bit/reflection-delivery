Implement import support for the subaccount environment instance resource in the SAP BTP Terraform provider. Ensure the import process uses a composite identifier and correct any existing bugs related to attribute naming and error messaging. Maintain consistency between imported and created states by handling internally managed fields appropriately.

*   Implement import functionality:
    *   Support import using a composite identifier: `<subaccount_id>,<environment_instance_id>`.
    *   Parse the identifier in the `ImportState` method to set the 'subaccount_id' and 'id' attributes correctly.
    *   Return an error message if the identifier format is incorrect: "Expected import identifier with format: subaccount_id,environment_instance_id. Got: <provided_id>".

*   Ensure state consistency:
    *   In the `Read` method, strip the 'status' field from the parameters string during import if the parameters attribute is null/empty.
    *   Ensure the imported state matches the state from a normal Terraform apply, with no unexpected differences.

*   Correct attribute naming bug:
    *   Use 'subaccount_id' instead of 'subaccount' when restoring subaccount information during import.

*   Implement helper function:
    *   Create `getEnvironmentInstanceIdForImport` in `internal/provider/resource_subaccount_environment_instance_test.go`.
        *   Accept a resource name string and return the import ID as `<subaccount_id>,<primary_resource_id>`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.