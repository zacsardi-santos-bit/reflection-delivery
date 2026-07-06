## Description

Kiali currently only shows clusters it has direct API access to. When a mesh spans multiple clusters but Kiali cannot connect to some of them (due to network isolation, missing credentials, or intentional separation), those clusters are completely invisible in the clusters listing. This makes it impossible for operators to see the full picture of their mesh, or to navigate to a separate Kiali instance running on an inaccessible cluster.

We need a way to configure clusters that Kiali "knows about" but cannot directly access, so they still appear in the cluster listing with any pre-configured Kiali URLs. Each cluster in the response should also carry an explicit flag indicating whether it is accessible, so UI clients can distinguish between clusters that can be actively managed and those that are navigable only through their own Kiali endpoint.

## Expected Behavior

- Operators can configure clusters that are part of the mesh but inaccessible to Kiali
- Configured inaccessible clusters appear in the clusters API response with a clear "not accessible" marker
- Configured Kiali instance URLs for those clusters are included in the response so the UI can link to them
- Accessible clusters are clearly marked as such
- Adding a Kiali instance URL for an already-known cluster does not create a duplicate cluster entry
- The cluster discovery works correctly even when Kiali runs with namespaced (non-cluster-wide) access

## Why This Matters

In multi-cluster deployments where network policies or security constraints prevent a single Kiali from having credentials for every cluster, administrators still want to surface all clusters and allow users to navigate between separate Kiali installations. Without this feature, operators have no way to expose knowledge of isolated clusters through the Kiali interface.
