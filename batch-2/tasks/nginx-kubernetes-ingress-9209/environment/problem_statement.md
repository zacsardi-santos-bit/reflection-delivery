## Description

When a policy is updated in the cluster, the ingress controller needs to identify which Ingress resources reference that policy so they can be reconfigured. Currently, the policy reference checker always reports that Ingress and Minion resources do not reference any policy, which means Ingress configurations are never refreshed when a policy changes. We need this lookup to actually parse the policies annotation on an Ingress and correctly determine whether a given policy (identified by namespace and name) is referenced.

Policies can be listed in an Ingress annotation as either a bare name (implying the same namespace as the Ingress) or as a fully qualified namespace-plus-name reference. The reference checker should handle both formats, as well as comma-separated lists of multiple policies.

## Expected Behavior

- The policy reference checker correctly returns true when an Ingress or Minion references the given policy, either by bare name or by namespaced reference.
- When a policy is listed by bare name, it only matches if the policy's namespace matches the Ingress's namespace.
- When a policy is listed as a comma-separated list, any matching entry should cause the method to return true.
- The annotation value should be validated to ensure each policy entry is a properly formed name: non-empty, matching DNS subdomain rules (lowercase alphanumeric, hyphens and dots, no leading/trailing hyphens or dots, no consecutive dots, within length limits).
- When a namespaced format is used, both the namespace and name parts are validated independently.
- The ingress annotation validation should be refactored to accept a single consolidated options object instead of a long list of individual boolean parameters.

## Why This Matters

Without proper reference tracking, changes to policies attached to Ingress resources are silently ignored, leaving Ingress configurations stale. Adding robust validation of policy annotation values also helps users catch misconfigured policies early rather than at runtime.
