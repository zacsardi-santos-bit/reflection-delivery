Implement a utility type for handling VMware hardware version strings and update the vSphere simulator to validate preconditions for VM hardware upgrades. Ensure the simulator correctly handles various upgrade scenarios, including power state, host maintenance mode, and version compatibility.

*   Define a new named string type `HardwareVersion` in `vim25/types/hardware_version.go`.
    *   Ensure type conversion from string to `HardwareVersion` and back preserves the original string.
    *   Implement `HardwareVersion.IsValid()` to return:
        *   `false` for empty strings, prefix-only strings (e.g., 'vmx-'), and numbers without the prefix (e.g., '13').
        *   `true` for valid 'vmx-N' patterns, case-insensitive (e.g., 'vmx-13', 'VMX-18').
    *   Implement `HardwareVersion.Int()` to return:
        *   The integer version number for valid strings.
        *   `0` for invalid strings.
    *   Implement `HardwareVersion.String()` to return:
        *   The canonical lowercase form 'vmx-N' for valid strings.
        *   An empty string for invalid inputs.

*   Update the VM upgrade operation in the simulator to validate preconditions:
    *   Return `InvalidPowerStateFault` if the VM is not powered off, with `ExistingState` set to the current power state and `RequestedState` set to powered off.
    *   Return `InvalidState` fault if the VM's host is in maintenance mode, with `FaultCause.LocalizedMessage` as '{hostMoRefValue} in maintenance mode'.
    *   Return `InvalidState` fault if the VM is a template, with `FaultCause.LocalizedMessage` as '{vmMoRefValue} is template'.
    *   Return `InvalidState` fault if the VM is already at the highest supported version and the target version is the same, with `FaultCause.LocalizedMessage` as '{vmMoRefValue} is latest version'.
    *   Return `NotSupported` fault if the requested version is unsupported in the environment or by the VM's host, with `FaultCause.LocalizedMessage` as '{version} not supported'.
    *   Return `AlreadyUpgradedFault` if the target version is less than or equal to the current version.
    *   Return `InvalidArgument` fault if the target version is supported but has an integer value less than 3.

*   Implement upgrade logic:
    *   Upgrade to the latest supported version if the target version is an empty string, updating `config.version` and `summary.config.hwVersion` to the latest version.
    *   After a successful upgrade to a specific version, update `config.version` and `summary.config.hwVersion` to the requested version in canonical lowercase form.

*   Update the command-line VM upgrade tool to:
    *   Return a failure exit code if the VM is already at the latest supported hardware version.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.