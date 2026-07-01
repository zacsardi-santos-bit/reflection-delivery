## Description

The SageMaker mock currently has no support for SageMaker HyperPod Cluster operations. Developers who write code that manages compute clusters on SageMaker cannot test that code locally or in CI without real AWS credentials, because the mock raises errors for any cluster-related API calls.

## Expected Behavior

The mock SageMaker service should support the following cluster operations:

- **Creating a cluster** — providing a cluster name, one or more instance groups (each with instance count, type, lifecycle configuration, execution role, and threads-per-core), VPC configuration, and optional tags should succeed and return the cluster's ARN.
- **Describing a cluster** — looking up a cluster by name or ARN should return the cluster's status (shown as active/in-service immediately after creation), its instance group details, and its VPC configuration.
- **Deleting a cluster** — removing a cluster should succeed and subsequent describe calls should return a not-found error.
- **Describing a single cluster node** — querying a specific node by its identifier should return node-level details including its status, instance type, lifecycle configuration, and threading information.
- **Listing clusters** — should return all clusters with filtering by name substring and sorting by name in ascending or descending order.
- **Listing cluster nodes** — should return all nodes across all instance groups, with optional filtering by instance group name substring. Each node should have a predictable identifier based on the group name and a zero-based index.
- **Tagging** — clusters should support AWS tagging; tags set at creation time and tags added afterwards should both be retrievable.

The mock should also enforce validation rules:
- Attempting to create a cluster with a name that already exists should be rejected with a resource-already-exists error.
- Attempting to create a cluster where a lifecycle configuration's storage path does not begin with the required prefix should be rejected with a validation error.

## Resource Tagging API Integration

SageMaker clusters should be discoverable through the Resource Groups Tagging API when filtering by the SageMaker service. A cluster's ARN and tags should appear in the results.

## Why This Matters

Without this mock support, any code path that creates or manages SageMaker HyperPod Clusters cannot be unit tested offline. Adding this support makes it possible to write comprehensive tests without needing live AWS infrastructure.
