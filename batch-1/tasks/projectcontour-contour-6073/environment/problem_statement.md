## Description

When deploying a Contour gateway via the provisioner, there is currently no way to restrict the gateway to only watch routes in a specific subset of namespaces. Every provisioned gateway ends up watching all namespaces, which may not be desirable in multi-tenant clusters where you want a gateway to serve traffic only for specific teams or environments.

Additionally, the RBAC resources created during provisioning do not support namespace-scoped permissions. There is no mechanism to create roles and role bindings tied to specific namespaces, and the cluster role cannot be restricted to only cluster-scoped permissions when finer-grained RBAC is needed.

## Expected Behavior

- It should be possible to configure a provisioned gateway with a list of namespaces to watch, so that the gateway only reconciles routes created in those namespaces.
- Routes created in namespaces not on the watch list should be ignored entirely — they should not appear as accepted or reconciled.
- Routes in watched namespaces should be accepted normally and traffic should flow through the gateway as expected.
- A utility should exist to convert the internal namespace type to plain strings, to make it easier to work with namespace values across the provisioner.
- RBAC roles and role bindings should be creatable with an explicit namespace scope, rather than always inheriting the Contour instance's namespace.
- The cluster role should support a mode where it contains only cluster-scoped permission rules (for gateway class and namespace resources), without including namespace-scoped resource rules.

## Why This Matters

Multi-tenant clusters often require that each gateway only sees traffic from specific namespaces. Without the ability to configure watched namespaces on a provisioned gateway, all routes in the cluster are visible to every gateway, which violates isolation boundaries. This feature allows operators to provision gateways with proper namespace-level isolation.
