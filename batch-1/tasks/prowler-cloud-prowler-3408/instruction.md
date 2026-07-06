Implement new security compliance checks for Azure virtual machines to enhance cloud security posture. Develop checks to verify disk encryption with customer-managed keys, ensure VMs use managed disks, and confirm endpoint protection installation. Create a service layer to retrieve and model Azure VM and disk data.

*   Implement the `VirtualMachines` service class:
    *   Use `ComputeManagementClient` for each subscription.
    *   Ensure `clients[subscription].__class__.__name__` equals `'ComputeManagementClient'`.
    *   Expose `virtual_machines` and `disks` attributes, populated by `__get_virtual_machines__` and `__get_disks__` methods.
*   Create `VirtualMachine` and `Disk` dataclasses:
    *   `VirtualMachine` must have `resource_id`, `resource_name`, and `storage_profile`.
    *   `Disk` must have `resource_id`, `resource_name`, `vms_attached`, and `encryption_type`.
*   Develop the `vm_ensure_attached_disks_encrypted_with_cmk` check:
    *   Iterate over `vm_client.disks` and skip disks with empty `vms_attached`.
    *   Return `PASS` if `encryption_type` is `'EncryptionAtRestWithCustomerKey'`, otherwise `FAIL`.
    *   Include `subscription`, `resource_name`, and `resource_id` in results.
*   Develop the `vm_ensure_unattached_disks_encrypted_with_cmk` check:
    *   Iterate over `vm_client.disks` and skip disks with non-empty `vms_attached`.
    *   Apply the same `PASS`/`FAIL` logic as the attached disks check.
*   Develop the `vm_ensure_using_managed_disks` check:
    *   Iterate over `vm_client.virtual_machines`.
    *   Return `PASS` if all disks are managed, otherwise `FAIL`.
    *   Use `vm_id` as `resource_id` in results.
*   Develop the `defender_assessments_vm_endpoint_protection_installed` check:
    *   Iterate over `defender_client.assessments`.
    *   Check for 'Install endpoint protection solution on virtual machines' key.
    *   Return `PASS` if status is 'Healthy', otherwise `FAIL`.
    *   Use `resource_name` and `resource_id` from the `Assesment` object.
*   Handle empty `disks` or `virtual_machines` dicts by returning an empty list from `execute()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.