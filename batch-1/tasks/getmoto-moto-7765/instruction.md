Implement support for SageMaker HyperPod Cluster operations in the moto SageMaker mock to enable local testing of cluster management code without AWS credentials. Ensure the mock can handle cluster creation, description, deletion, node inspection, and listing, with full tagging support and validation rules.

*   Implement `create_cluster` in `moto/sagemaker/models.py` and `responses.py`:
    *   Accept parameters: `ClusterName`, `InstanceGroups`, `VpcConfig`, `Tags`.
    *   Return a dictionary with `ClusterArn`.
    *   Raise `ResourceInUse` error if a cluster with the same name exists.
    *   Raise `ValidationException` if any `LifeCycleConfig.SourceS3Uri` does not start with "s3://sagemaker".

*   Implement `describe_cluster` in `moto/sagemaker/models.py` and `responses.py`:
    *   Accept `ClusterName` as name or ARN.
    *   Return a dictionary with `ClusterArn`, `ClusterName`, `ClusterStatus` as "InService", `InstanceGroups`, and `VpcConfig`.
    *   Raise `ValidationException` if the cluster is not found.

*   Implement `delete_cluster` in `moto/sagemaker/models.py` and `responses.py`:
    *   Accept `ClusterName` as name or ARN.
    *   Return a dictionary with `ClusterArn`.
    *   Ensure subsequent `describe_cluster` calls raise `ValidationException` with "Could not find cluster '{cluster_name}'".

*   Implement `describe_cluster_node` in `moto/sagemaker/models.py` and `responses.py`:
    *   Accept `ClusterName` and `NodeId`.
    *   Return a dictionary with `NodeDetails` including `InstanceGroupName`, `InstanceId`, `InstanceStatus`, `InstanceType`, `LifeCycleConfig`, and `ThreadsPerCore`.

*   Implement `list_clusters` in `moto/sagemaker/models.py` and `responses.py`:
    *   Return `ClusterSummaries` with `ClusterArn`, `ClusterName`, `ClusterStatus`.
    *   Support `NameContains`, `SortBy`, and `SortOrder` parameters.

*   Implement `list_cluster_nodes` in `moto/sagemaker/models.py` and `responses.py`:
    *   Accept `ClusterName` and optional `InstanceGroupNameContains`.
    *   Return `ClusterNodeSummaries` with `InstanceGroupName`, `InstanceId`, `InstanceType`, and `InstanceStatus`.

*   Ensure tagging support:
    *   Store and retrieve tags using `list_tags`.
    *   Support adding tags via `add_tags` using the cluster ARN.

*   Enforce validation rules:
    *   Raise `ResourceInUse` error for duplicate cluster names.
    *   Raise `ValidationException` for invalid `SourceS3Uri`.

*   Integrate with Resource Groups Tagging API:
    *   Ensure clusters appear in `get_resources` response when `ResourceTypeFilters` includes "sagemaker".
    *   Include cluster ARN and tags in `ResourceTagMappingList`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.