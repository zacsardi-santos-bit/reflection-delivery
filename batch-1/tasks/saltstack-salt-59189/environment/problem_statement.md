## Description

The virtual machine update function in the virtualization module has two bugs that cause incorrect behavior.

**Bug 1: Unnecessary UEFI redefinition**

When a VM is already configured with UEFI firmware — meaning it was previously set up with explicit loader and variable-store paths — calling the update function with the "use EFI automatically" option should recognize that the VM is already in the correct state and skip the update. Instead, the current code marks the definition as changed and re-submits it to the hypervisor even though nothing actually changed. This causes spurious "definition changed" results for UEFI VMs that are already correctly configured.

**Bug 2: Serial and console device updates silently ignored**

When an operator passes updated serial port or console device definitions to the VM update function, those devices are silently ignored due to a parameter naming mismatch inside the function. The result is that serial/console updates appear to do nothing, and the return value from the function is structured incorrectly.

## Expected Behavior

- Calling the update function with automatic EFI boot enabled on a VM that already has a pflash UEFI loader configured should report no definition change and not re-submit the domain definition to the hypervisor.
- Calling the update function with updated serial and console device lists should apply those changes and report the definition as updated, along with the standard disk and interface change information (even when no disk or NIC changes were requested).

## Why This Matters

These bugs make the update function unreliable: UEFI VMs appear to be modified on every update run even when they haven't changed, and serial/console device type changes can never be applied through the update function.
