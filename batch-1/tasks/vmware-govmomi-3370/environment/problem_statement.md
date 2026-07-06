## Description

The virtual machine hardware upgrade operation in the vSphere simulator does not properly validate preconditions before attempting an upgrade. Real vSphere enforces several checks — the VM must be powered off, not a template, its host must not be in maintenance mode, the requested hardware version must be supported by the environment and by the specific host, and the VM must not already be at or beyond the requested version. The simulator currently ignores all of these and either silently succeeds or produces the wrong error, making it unreliable for testing code that depends on correct upgrade behavior.

Additionally, there is no utility for parsing and comparing hardware version strings (the "vmx-N" format used by VMware). Such a utility is needed to support the validation logic and to allow callers to compare, normalize, and extract numeric values from version strings.

Finally, the command-line upgrade tool reports success even when a VM is already at the latest hardware version, which is misleading and inconsistent with real vSphere behavior.

## Expected Behavior

- Attempting to upgrade a powered-on VM should fail with an appropriate power state error.
- Attempting to upgrade a VM whose host is in maintenance mode should fail with a descriptive state error.
- Attempting to upgrade a template VM should fail with a state error.
- Attempting to upgrade to an unsupported version (either unsupported in the environment or unsupported on the specific host) should fail with a "not supported" error and an informative message.
- Attempting to upgrade to a version equal to or lower than the VM's current version should fail with an "already upgraded" error.
- Attempting to upgrade to a version that is too old (below a minimum threshold) should fail with an invalid argument error.
- Upgrading with no target version specified should upgrade to the latest supported version, updating both the config version and the hardware version summary fields.
- The command-line upgrade tool should report failure when the VM is already at the latest supported hardware version.

## Why This Matters

Without correct error handling, code that tests upgrade flows against the simulator will not catch bugs that would surface against a real vSphere environment. Proper validation makes the simulator a trustworthy test double.
