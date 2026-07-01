Implement a new resource type for SageMaker HyperPod clusters in Cloud Custodian to enable policy management. Ensure this resource type allows listing, filtering, tagging, and deleting clusters, while handling non-existent clusters gracefully.

*   Register a new resource type 'sagemaker-cluster':
    *   Implement as the `Cluster` class in `c7n/resources/sagemaker.py`.
    *   Map as 'aws.sagemaker-cluster' in `c7n/resources/resource_map.py`.
    *   Define an inner class `resource_type` with:
        *   `service = 'sagemaker'`
        *   `enum_spec = ('list_clusters', 'ClusterSummaries', None)`
        *   `detail_spec = ('describe_cluster', 'ClusterName', 'ClusterName', None)`
        *   `arn = id = 'ClusterArn'`
        *   `name = 'ClusterName'`
        *   `date = 'CreationTime'`
        *   `cfn_type = None`
        *   `permission_prefix = 'sagemaker'`
        *   `universal_taggable = object()`

*   Implement cluster enumeration and augmentation:
    *   Use `list_clusters` API for enumeration.
    *   Use `describe_cluster` API for full details, with `ClusterName` as the key.
    *   Expose `ClusterArn` and `ClusterName` fields.

*   Implement universal tagging:
    *   Augment resources with tag data using a custom describe source.
    *   Create `SagemakerClusterDescribe` class extending `DescribeSource` to support universal tag augmentation.

*   Implement policy actions:
    *   Support 'tag' action to apply tags using `ClusterArn`.
    *   Support 'remove-tag' action to remove tags by key name.
    *   Implement 'delete' action as `ClusterDelete` class:
        *   Call `DeleteCluster` API using `ClusterName`.
        *   Handle `ResourceNotFound` exceptions gracefully.

*   Implement filters:
    *   Create `ClusterSubnetFilter` class extending `SubnetFilter`:
        *   Use `VpcConfig.Subnets[]` for subnet ID extraction.
    *   Create `ClusterSecurityGroupFilter` class extending `SecurityGroupFilter`:
        *   Use `VpcConfig.SecurityGroupIds[]` for security group ID extraction.

*   Update resource metadata validation:
    *   Add 'sagemaker-cluster' to the exclusion list in `test_resource_type_empty_metadata` due to `cfn_type = None`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.