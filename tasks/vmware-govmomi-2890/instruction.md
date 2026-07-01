Ensure the virtual machine simulator correctly normalizes virtual disk capacity when adding a disk with incomplete or conflicting capacity fields. Implement logic to synchronize the byte and kilobyte capacity fields based on specified conditions.

*   Update the simulator to handle capacity normalization in `simulator/virtual_machine.go` for the `VirtualDisk` device type:
    *   When only `CapacityInBytes` is set and `CapacityInKB` is zero:
        *   Store `CapacityInBytes` as provided.
        *   Calculate and store `CapacityInKB` as `CapacityInBytes / 1024`.
    *   When only `CapacityInKB` is set and `CapacityInBytes` is zero:
        *   Store `CapacityInKB` as provided.
        *   Calculate and store `CapacityInBytes` as `CapacityInKB * 1024`.
    *   When both `CapacityInBytes` and `CapacityInKB` are set to consistent values:
        *   Preserve both fields as-is.
    *   When both fields are set to conflicting values:
        *   Preserve `CapacityInBytes` as provided.
        *   Recalculate and store `CapacityInKB` as `CapacityInBytes / 1024`.

*   Ensure the normalization logic is applied during the device configuration process so that the reconciled values are reflected when querying the VM's devices.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.