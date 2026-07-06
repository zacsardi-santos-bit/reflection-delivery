Implement support for AWS edge infrastructure locations in your Kubernetes cluster provider. Ensure that subnets in edge locations are correctly identified and handled differently from those in standard availability zones, particularly in routing and resource allocation.

*   Update the `SubnetSpec` type:
    *   Add optional pointer fields `ZoneType` and `ParentZoneName`.
    *   Implement `IsEdge()` to return true if `ZoneType` is "local-zone".
    *   Implement `SetZoneInfo(zones []*ec2.AvailabilityZone) error` to set `ZoneType` and `ParentZoneName` based on AWS data.

*   Modify subnet ID retrieval:
    *   `Subnets.IDs()` should return IDs of non-edge subnets only.
    *   `Subnets.IDsWithEdge()` should return IDs of all subnets, including edge zones.

*   Adjust subnet filtering:
    *   `Subnets.FilterPrivate()` should exclude edge-zone subnets.
    *   `Subnets.FilterPublic()` should exclude edge-zone subnets.

*   Enhance zone and routing logic:
    *   `Subnets.GetUniqueZones()` should include all subnets.
    *   `Service.retrieveZoneInfo(zoneNames []string)` should handle AWS zone queries.
    *   Update `Service.getNatGatewayForSubnet` to handle edge subnets differently, prioritizing parent zone NAT gateways.

*   Update route table logic:
    *   Public IPv4 edge subnets should route to the internet gateway.
    *   Private IPv4 edge subnets should route to the parent zone's NAT gateway.
    *   Return an error for IPv6 subnets in edge zones.

*   Error handling and tagging:
    *   Update error messages for missing internet gateways and unsupported IPv6 in edge zones.
    *   Ensure edge-zone subnets do not receive load balancer role tags.
    *   Populate `ZoneType` for subnets discovered in unmanaged VPCs.

*   Ensure reconciliation retrieves and stores zone information for each subnet. Handle errors if zone attributes are not populated.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.