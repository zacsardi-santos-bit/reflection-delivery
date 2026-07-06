## Description

The Azure Stack HCI Cluster resource in the Terraform provider does not currently support assigning an automanage configuration profile to the cluster. Users who want to manage automanage configuration assignments on their HCI clusters through Terraform have no way to do so today.

Additionally, the provider is missing the ID parsing and validation utilities needed to handle the specific resource ID format used for automanage configuration assignments scoped to Azure Stack HCI clusters. This ID format differs from the format used for regular VM assignments, so existing parsing utilities cannot be reused.

## Expected Behavior

- The Stack HCI Cluster resource should accept an optional automanage configuration ID attribute, allowing users to associate an automanage configuration profile with a cluster during creation or update.
- Users should be able to remove the automanage configuration assignment from a cluster in a subsequent apply.
- The provider should include proper parsing support for automanage configuration assignment IDs that are scoped to HCI clusters, correctly extracting the subscription, resource group, cluster name, and configuration profile assignment name from the ID string.
- The provider should include validation support for these HCI-scoped automanage configuration assignment IDs, rejecting empty, incomplete, or incorrectly-cased IDs.

## Why This Matters

Many Azure customers use automanage to automate machine best-practice configurations. Without this support, customers cannot manage automanage configuration assignments on their HCI clusters using Terraform, forcing them to rely on manual or out-of-band processes. Adding this capability makes HCI cluster lifecycle management fully declarative within Terraform.
