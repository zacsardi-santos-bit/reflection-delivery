## Description

When a user creates a new Podman virtual machine on macOS or Windows, the container registry configuration from their host system is not automatically made available inside the VM. This means that any custom registries, mirrors, or registry authentication settings the user has configured on their host are silently absent inside the newly created VM, leading to confusing failures when trying to pull images from non-default registries.

## Expected Behavior

- When creating a new Podman machine, the host's container registry configuration file should automatically be linked into the correct location inside the VM.
- An automation script (playbook) should be generated and passed to the machine initialization process so that the VM bootstraps with the host's registry settings in place.
- On macOS, the path to the registry configuration file inside the VM should be the same as the host path.
- On Windows, the host path should be translated from Windows-style notation into the Linux-compatible path format used inside the VM (e.g., drive letters are converted to the appropriate mount point prefix).
- The generated script should be written to a temporary directory and referenced by the correct filename.

## Why This Matters

Users who have customized their container registry configuration on the host currently need to manually replicate that setup inside every new VM they create. Automating this synchronization reduces friction, prevents image pull failures due to missing registry configuration, and ensures a consistent experience between the host and the VM from the moment the machine is first initialized.
