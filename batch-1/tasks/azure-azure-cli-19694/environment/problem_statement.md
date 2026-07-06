## Description

When updating an existing AKS cluster, users have no way to attach or detach an Azure Container Registry (ACR) through the update command. This functionality exists only for the create flow, leaving users who want to manage ACR access for an existing cluster without a good option.

A key prerequisite for supporting ACR attach/detach during updates is knowing how to identify the correct client ID for the cluster — clusters using managed identity need the client ID from the kubelet identity profile, while clusters using service principals get it from the service principal profile. Currently, neither a utility to detect which authentication type a cluster uses nor a method to retrieve the appropriate client ID exists in the update path.

## Expected Behavior

- A utility function should be available to determine whether a given cluster object is using managed service identity (as opposed to a service principal). It should handle edge cases like a missing or null cluster object gracefully.
- A method should exist to resolve the client ID of a cluster regardless of whether it uses managed identity or a service principal, raising an appropriate error if no client ID can be found.
- The update decorator should support an attach/detach ACR step that uses the resolved client ID and subscription ID to call the underlying ACR permissions helper for both attach and detach operations.
- The context should expose a way to retrieve the "detach ACR" parameter in update mode, similar to how "attach ACR" is already accessible.

## Why This Matters

Without this support, operators managing long-lived clusters must resort to manual role assignments or recreate clusters just to change ACR access. Adding ACR attach/detach support to the update flow makes day-2 cluster management significantly more ergonomic.
