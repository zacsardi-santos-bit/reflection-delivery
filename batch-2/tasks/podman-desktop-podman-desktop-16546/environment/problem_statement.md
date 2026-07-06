## Description

When Podman runs inside a virtual machine (as it does on macOS and Windows), host system certificates are not automatically available inside the VM. This causes failures when Podman tries to connect to container registries or internal services that require custom or corporate CA certificates to be trusted. There's currently no mechanism to synchronize host certificates into running Podman machines.

## Expected Behavior

- A new certificate synchronization capability should be introduced for the Podman extension.
- The synchronization should collect trusted certificates from all certificate stores available on the host (system-level, bundled, and any extra/user-installed certificates), and deduplicate them.
- The system should determine which Podman machines are currently running and have a VM (non-native Linux), and synchronize certificates into each of them.
- Only certificates that are not already present in the VM should be uploaded; certificates on the VM that are no longer on the host should be removed.
- If no changes are needed, the operation should skip the trust store update and service restart steps.
- If no running machines are found, a warning should be displayed to the user.
- Synchronization of one machine failing should not prevent other machines from being synchronized.
- The operation should show progress feedback to the user and support cancellation.

## Why This Matters

Corporate and enterprise environments often require custom root CAs. Without this sync, Podman inside a VM cannot verify TLS certificates for internal registries, breaking core workflows like pulling images or logging in. This feature makes Podman in VM environments work seamlessly with the host's certificate trust store.
