Implement support for additional filters for AWS MemoryDB resources in Cloud Custodian. Add filters for security groups, subnets, KMS keys, and network-location to enhance policy enforcement capabilities.

*   Implement a 'kms-key' filter for MemoryDB clusters:
    *   Extend `KmsRelatedFilter` in `c7n/resources/memorydb.py`.
    *   Set `RelatedIdsExpression = 'KmsKeyId'`.
    *   Register under the name 'kms-key' in the MemoryDb filter registry.
    *   Ensure the filter supports attributes like 'c7n:AliasName' for KMS key alias matching.

*   Implement a 'security-group' filter for MemoryDB clusters:
    *   Extend `net_filters.SecurityGroupFilter` in `c7n/resources/memorydb.py`.
    *   Set `RelatedIdsExpression = "SecurityGroups[].SecurityGroupId"`.
    *   Register under the name 'security-group' in the MemoryDb filter registry.
    *   Enable filtering by security group attributes such as tags.

*   Implement a 'network-location' filter for MemoryDB clusters:
    *   Register `net_filters.NetworkLocation` directly in `c7n/resources/memorydb.py`.
    *   Use the name 'network-location' in the MemoryDb filter registry.

*   Implement a 'subnet' filter for MemoryDB clusters:
    *   Extend `net_filters.SubnetFilter` in `c7n/resources/memorydb.py`.
    *   Set `RelatedIdsExpression = ""`.
    *   Retrieve subnet groups from the 'memorydb-subnet-group' resource manager.
    *   Extract subnet IDs from the `Subnets[].Identifier` field of each matching group.
    *   Register under the name 'subnet' in the MemoryDb filter registry.
    *   Implement methods:
        *   `get_subnet_groups(self) -> dict`: Returns `{name: group_dict}`.
        *   `get_related_ids(self, resources) -> set`: Fetches subnet IDs.
        *   `process(self, resources, event=None) -> list`: Populates `self.groups` and calls `super().process()`.

*   Add a new resource type 'memorydb-subnet-group':
    *   Implement `MemoryDbSubnetGroup` as a `QueryResourceManager` in `c7n/resources/memorydb.py`.
    *   Register with `@resources.register('memorydb-subnet-group')`.
    *   Define inner class `resource_type` (TypeInfo) with:
        *   `service = 'memorydb'`
        *   `arn_type = 'subnetgroup'`
        *   `enum_spec = ('describe_subnet_groups', 'SubnetGroups', None)`
        *   `name = id = 'Name'`
        *   `filter_name = 'SubnetGroupName'`
        *   `filter_type = 'scalar'`
        *   `cfn_type = 'AWS::MemoryDB::SubnetGroup'`
        *   `universal_taggable = object()`
        *   `permissions = ('memorydb:DescribeSubnetGroups',)`
    *   Set `augment = universal_augment`.

*   Update the resource map:
    *   Add `"aws.memorydb-subnet-group": "c7n.resources.memorydb.MemoryDbSubnetGroup"` to the `ResourceMap` dictionary in `c7n/resources/resource_map.py`, after the "aws.memorydb" entry.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.