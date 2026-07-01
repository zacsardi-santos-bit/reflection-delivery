## Description

The mesh service proxy currently supports declaring reachable services using a tag-based approach, but it lacks support for specifying reachable backends by direct resource reference (name, namespace, or label selectors). Operators who want to limit which specific named backend resources a workload can reach — such as individual services, external services, or multi-zone services — have no way to express this intent. Additionally, the outbound backend reference validation does not yet recognize a third resource type that is now supported in the mesh, meaning configurations referencing it will produce incorrect error messages.

## Expected Behavior

- When a dataplane configuration lists explicit backend references for transparent proxy mode, the system should validate those references for correctness: valid resource kinds, proper use of name vs. labels, RFC 1123 name format, and that namespace is only used alongside a name.
- The reachability graph should be able to evaluate whether a source workload can reach a specific named backend resource, based on traffic permission policies applied to that backend.
- The traffic permission evaluation for named backends should integrate with the existing policy rules engine, correctly handling allow, deny, and shadow-deny actions.
- The valid kinds for outbound backend references must include all three supported resource types, and validation error messages should reflect this complete list.

## Why This Matters

Without this capability, operators must either allow all traffic (which can significantly degrade mesh performance at scale) or rely on coarse-grained service tag matching. Being able to declare which specific named backends a service needs to reach, and having the system enforce traffic permissions against those named backends, dramatically improves both the performance and the correctness of mesh traffic control in deployments with many services.
