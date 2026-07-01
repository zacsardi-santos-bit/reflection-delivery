## Description

Cloud Custodian currently supports governance for many SageMaker resources (notebooks, models, endpoints, domains), but it has no support for the newer SageMaker HyperPod cluster service. This gap means organizations cannot use their existing Cloud Custodian policies to audit, tag, or clean up HyperPod clusters — they're effectively invisible to the policy engine.

## Expected Behavior

- Users should be able to define Cloud Custodian policies targeting SageMaker HyperPod clusters as a first-class resource type.
- The resource type should support listing all clusters and retrieving full details for each one.
- Users should be able to filter clusters by their tags (including detecting absent tags), by name/attributes, and by their network configuration — specifically which subnets and security groups the cluster is associated with.
- Users should be able to apply tags to clusters, remove tags from clusters, and delete clusters through policy actions.
- The delete action should gracefully handle clusters that no longer exist at the time the action runs.

## Why This Matters

Security and operations teams rely on Cloud Custodian to enforce tagging standards, detect non-compliant network placements, and clean up unused resources. Without support for HyperPod clusters, these teams have a blind spot for an increasingly important compute service. Adding this resource type brings HyperPod clusters under the same governance umbrella as other SageMaker resources.
