## Description

Kustomize needs a built-in transformer plugin for namespace transformation that correctly handles the full range of Kubernetes resource types, including cluster-scoped resources and cross-resource namespace references.

## Expected Behavior

- When a namespace transformer is applied, namespace-scoped resources (such as config maps and service accounts) should have the target namespace set or overwritten.
- Cluster-level resources — those that cannot belong to a namespace, including namespace declarations, custom resource definitions, cluster roles, cluster role bindings, and persistent volumes — must not have their metadata namespace modified.
- Role bindings that reference service accounts by namespace should have those namespace references updated, but only for service accounts that are part of the set of resources being transformed. References to service accounts outside the transformed set must be left unchanged.
- Role binding resources that have no subject entries must be handled gracefully without causing a crash.

## Why This Matters

Without this plugin, the namespace transformation either skips the nuanced handling described above or relies on a legacy code path that cannot be configured or extended via the plugin system. Moving this functionality to a proper builtin plugin makes it consistent with other transformers, allows it to be configured via the standard plugin API, and fixes a crash that occurs when a role binding has no subjects.
