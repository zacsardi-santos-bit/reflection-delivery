## Description

The AKS cluster update command supports updating many cluster properties, but it currently lacks the ability to update the managed identity configuration of an existing cluster. Users who want to switch their cluster from a service principal to managed identity, or switch between system-assigned and user-assigned managed identity types, have no supported path through the update command.

## Expected Behavior

- When updating an AKS cluster, the update flow should support changing the cluster's identity type.
- If a user provides an identity assignment target without also requesting managed identity to be enabled, the command should reject the request with an appropriate error.
- If the identity type would change as a result of the update, the user should be prompted to confirm the operation before it proceeds.
- If the user declines the confirmation, the operation should exit gracefully without making changes.
- When the user confirms (or bypasses the prompt with a flag), the cluster's identity should be updated to reflect the new configuration:
  - Providing a specific identity resource should result in a user-assigned managed identity.
  - Enabling managed identity without specifying a resource should result in a system-assigned managed identity.

## Why This Matters

Cluster operators need to be able to evolve their identity strategy over time — for example, migrating from a service principal to a managed identity as security requirements change. Without update support for identity, users are forced to recreate clusters or use workarounds, which is disruptive and error-prone.
