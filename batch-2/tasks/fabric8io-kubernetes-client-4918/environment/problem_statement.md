## Description

The Kubernetes client library resolves service URLs through several mechanisms: platform-injected environment variables, ingress resources, and service annotations. However, the ingress-based URL resolver only considers the older "extensions" ingress API group and completely ignores the newer networking API group that is used by modern Kubernetes clusters. This means users on recent Kubernetes versions cannot get valid service URLs through the ingress resolution path — the resolver silently fails to find any ingress and returns nothing.

Additionally, there is no unit test coverage for the individual URL resolution helper methods, making it hard to verify edge cases and maintain correct behavior.

## Expected Behavior

- When resolving a service URL via ingress resources, the client should check which ingress API group the cluster actually supports (the newer networking group or the older extensions group) and use that group accordingly.
- When an ingress in the networking API group has TLS configured for the matching host, the returned URL should use HTTPS; otherwise it should use HTTP.
- If neither ingress API group is supported by the cluster, the resolver should return nothing instead of throwing an error.
- Helper utilities for looking up host, port, and protocol from environment variables or system properties should work correctly and return the right values.
- Resolving a URL when no matching port name exists in the service should throw a clear error identifying the missing port and service name.
- Looking up a service port by name should return nothing for an empty port list, the first port for an empty port-name query, and the matching port otherwise.

## Why This Matters

Users running workloads on modern Kubernetes clusters rely on the networking API group for ingress definitions. Without this fix, the client library silently ignores those ingress resources and cannot produce service URLs for them. Proper unit tests also make it much easier to maintain and extend this functionality confidently.
