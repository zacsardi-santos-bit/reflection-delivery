## Description

When the mirrord agent runs as an ephemeral container in a Kubernetes pod that is part of a service mesh, all network traffic in that pod is intercepted by the mesh's sidecar proxy — including the agent's own communication traffic. This causes the agent's port to be routed through the mesh proxy, which is unintended and can break the agent's communication with clients.

There is currently no mechanism to tell the mesh proxy to leave the agent's communication port alone. We need an option to exclude the agent's port from the sidecar proxy's routing rules when it is deployed as an ephemeral container in a service mesh environment.

## Expected Behavior

- When the mesh exclusion option is enabled, a dedicated IPTables chain must be created to hold exclusion rules.
- This chain must be jumped to from the packet pre-routing hook before other redirect rules are applied.
- The chain must contain rules that accept (bypass proxy routing for) specific ports.
- The new chain name must be tracked as a recognized mirrord-managed chain so that it is included in cleanup operations.
- The system must support both creating new rules and loading existing ones during agent lifecycle management.

## Why This Matters

Teams running mirrord in service mesh environments (such as those using Istio ambient mode or Linkerd) encounter issues where the agent's own traffic is intercepted by the mesh sidecar. This leads to connectivity problems between the agent and its clients. Making it possible to opt out of proxy interception for the agent's port resolves these deployment failures.
