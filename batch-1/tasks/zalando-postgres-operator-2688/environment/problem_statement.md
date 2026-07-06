# Add Support for Owner References on Postgres Cluster Child Resources

## Description

When a managed Postgres cluster's custom resource is deleted, child resources such as services, endpoints, stateful sets, secrets, pod disruption budgets, and backup jobs are cleaned up by the operator. However, if the operator is in a broken state, has not fully synced, or the initial cluster creation failed, these child resources can be orphaned and require manual cleanup.

Additionally, Kubernetes-based monitoring and GitOps tools that rely on native ownership relationships cannot currently determine which child resources belong to which Postgres cluster, reducing observability.

## Requested Feature

Add an optional configuration toggle that, when enabled, sets native Kubernetes ownership relationships between the Postgres custom resource and all its managed child resources. This enables:

- **Cascading deletion**: When the parent Postgres resource is deleted, Kubernetes automatically removes all owned child resources.
- **Ownership visibility**: Monitoring and GitOps tooling can navigate ownership graphs natively.
- **Dynamic updates**: When the toggle is enabled or disabled, the operator should update ownership metadata on existing resources during its next sync cycle.

## Exceptions

The following child resources must NOT receive owner references, due to Kubernetes design constraints:

- Persistent Volume Claims (handled by StatefulSet reclaim policy)
- Patroni configuration service and endpoint (managed by Patroni itself)
- Cross-namespace secrets (owner references across namespaces are not allowed)

## Expected Behavior

- The feature is **disabled by default**.
- When enabled, all eligible child resources report the Postgres custom resource as their owner/controller.
- When disabled (or toggled off), child resources should have no operator-set ownership reference pointing to the Postgres resource.
- Existing resource comparison logic must detect mismatches in ownership metadata and trigger updates accordingly.

## Why This Matters

Without this feature, broken or partially-synced clusters may leave behind orphaned resources. Enabling owner references provides a more reliable and Kubernetes-idiomatic cleanup mechanism and improves integration with cluster management tooling.
