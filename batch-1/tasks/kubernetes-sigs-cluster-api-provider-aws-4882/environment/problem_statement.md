## Description

Currently, the AWS cluster provider has no awareness of AWS edge infrastructure locations (such as Local Zones). When a user configures cluster subnets in these edge locations alongside standard availability zones, the system treats all subnets identically — it does not record zone type information on subnet objects, and routing decisions ignore the distinct characteristics of edge zones. This causes incorrect behavior: edge-zone subnets may be selected for core infrastructure resources that do not support them (like load balancers and NAT gateways), and routing for private subnets in edge zones cannot correctly delegate to a parent-zone NAT gateway.

## Expected Behavior

- The system should query AWS to determine the zone type (standard availability zone vs. edge location) and parent zone for each subnet, and store that information on the subnet object.
- Methods that return subnet IDs for core infrastructure use should exclude edge-zone subnets; a separate method should be available to retrieve IDs across all zone types.
- Filtering subnets by public/private visibility should also exclude edge-zone subnets from results used for core infrastructure.
- Private subnets in edge zones should route traffic through the NAT gateway in their parent zone (or any available NAT gateway as a fallback), rather than expecting a dedicated NAT gateway in the edge zone.
- Public and private IPv4 subnets in edge zones should receive appropriate routes to an internet or NAT gateway. IPv6 subnets in edge zones should be rejected with a clear error since IPv6 is not broadly supported in those locations.
- Edge-zone subnets should not receive load balancer role tags that would cause the cloud provider to select them for load balancer creation.

## Why This Matters

Without this support, operators cannot extend their cluster network into AWS Local Zones. Any attempt to include edge-zone subnets in a cluster configuration either silently misroutes traffic or selects inappropriate infrastructure. This change enables clusters to span both standard availability zones and edge locations with correct routing and resource placement.
