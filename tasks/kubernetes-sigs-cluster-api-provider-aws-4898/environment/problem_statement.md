## Description

Currently, the EKS managed control plane only supports associating a single secondary CIDR block with its VPC. This is not enough for teams that need to attach multiple additional IP ranges to a cluster — for example, when using a dedicated IP space for pod networking alongside the primary VPC range.

## Expected Behavior

- The network configuration for a managed control plane should accept a list of additional CIDR blocks (not just one), and the provider should automatically associate all of them with the cluster's VPC at creation time.
- When reconciling, only CIDR blocks that are not already associated should be added — existing associations should be left untouched.
- When a cluster is deleted, all of the listed additional CIDR blocks that are currently associated with the VPC should be disassociated.
- If a cluster definition uses both the old single-CIDR field and the new list field at the same time, the single CIDR value must be present in the list. If it is not, the cluster resource should be rejected at admission time with a clear error that identifies the missing entry and tells the user where to add it.

## Why This Matters

Without this capability, users who need multiple IP ranges associated with their EKS VPC (a common requirement for certain CNI configurations) have no supported path. Adding support for a list of secondary CIDR blocks unlocks these networking setups while maintaining backward compatibility with existing single-CIDR configurations.
