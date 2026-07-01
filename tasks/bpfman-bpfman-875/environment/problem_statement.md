## Description

The uprobe program controller has two related issues that need to be resolved. First, the spec for an uprobe program currently accepts a list of targets, but in practice only a single target makes sense per program. The API should be simplified to accept a single target value rather than a list. Second, the controller currently always sends a container process identifier of zero with every uprobe attachment request, even when no container-level attachment is intended. This placeholder value should not be sent when no container is being targeted.

Additionally, to support attaching uprobes inside specific containers, the agent needs a way to discover which pods are running on a given node that match a label-based selector. This pod discovery capability is a foundational building block for container-aware uprobe attachment.

## Expected Behavior

- The uprobe program spec should accept a single target (string) rather than a list of targets.
- When constructing an uprobe attachment request without a container selector, the container process ID field should be omitted entirely.
- A pod lookup function should be available that, given a label selector, a namespace, and a node name, returns all matching pods running on that node.
- When no namespace is specified in the selector, the lookup should search across all namespaces.

## Why This Matters

Simplifying the target field reduces API confusion, and removing the spurious zero container PID prevents unintended behavior in the daemon. The pod discovery capability enables the controller to attach uprobes inside containers without requiring manual PID management.
