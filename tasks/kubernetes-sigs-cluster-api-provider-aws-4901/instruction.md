Implement support for AWS Wavelength Zones in the Kubernetes cluster networking provider. Ensure that Wavelength Zones are recognized as a distinct zone type and that networking resources are correctly provisioned in these zones.

*   Define a new constant `ZoneTypeWavelengthZone` with the string value "wavelength-zone" in `api/v1beta2/network_types.go`.
*   Update `SubnetSpec` methods:
    *   `IsEdge()` should return true for subnets with `ZoneType` as 'wavelength-zone'.
    *   Implement `IsEdgeWavelength()` to return true only for subnets with `ZoneType` as 'wavelength-zone'.
    *   `SetZoneInfo()` should populate `ZoneType` and `ParentZoneName` for 'wavelength-zone' metadata.
*   Modify `Subnets` methods:
    *   `IDs()` should exclude subnets in Wavelength Zones.
    *   `IDsWithEdge()` should include Wavelength Zone subnets.
    *   `GetUniqueZones()` should include Wavelength Zone names.
    *   Implement `HasPublicSubnetWavelength()` to check for public subnets in Wavelength Zones.
*   Add a `CarrierGatewayID` field to `VPCSpec` in `api/v1beta2/network_types.go`.
*   Implement `reconcileCarrierGateway()` in `pkg/cloud/services/network/carriergateways.go`:
    *   Check for an existing carrier gateway using `DescribeCarrierGatewaysWithContext`.
    *   Tag existing gateways or create a new one using `CreateCarrierGatewayWithContext` if none exists.
*   Implement `deleteCarrierGateway()` in `pkg/cloud/services/network/carriergateways.go`:
    *   Skip deletion for unmanaged VPCs.
    *   Use `DescribeCarrierGatewaysWithContext` to check for existing gateways and `DeleteCarrierGatewayWithContext` to delete if found.
*   Update `getRoutesForSubnet()` to handle Wavelength Zone subnets:
    *   For public IPv4 subnets, use `CarrierGatewayID` for routing. Return an error if `CarrierGatewayID` is missing.
    *   Return an error for IPv6 subnets in Wavelength Zones.
    *   Route private IPv4 subnets through the NAT gateway of their parent zone.
*   Ensure NAT gateway lookup supports Wavelength Zone subnets, returning the NAT gateway from the parent zone if available.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.