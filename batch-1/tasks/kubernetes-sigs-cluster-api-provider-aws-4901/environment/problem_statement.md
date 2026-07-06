## Description

AWS supports multiple types of edge zones beyond standard availability zones, including Wavelength Zones, which are designed to deliver ultra-low latency to mobile users through carrier infrastructure. Currently, the AWS cluster provider only recognizes standard availability zones and Local Zones — Wavelength Zones are not treated as a distinct zone type, so clusters cannot be configured to provision networking resources in them correctly.

## Expected Behavior

- Wavelength Zone subnets should be recognized as a distinct edge zone type, separate from Local Zones and standard availability zones.
- A method should exist to check whether a subnet belongs specifically to a Wavelength Zone (distinct from the general "is edge" check).
- Subnet listing operations that include edge zones should include Wavelength Zone subnets, while operations that exclude edge zones should continue to exclude them.
- Setting zone information on a subnet should work correctly for Wavelength Zones, including populating the zone type and parent zone name.
- It should be possible to check whether a collection of subnets contains any public Wavelength Zone subnet.
- The VPC configuration should be able to store a carrier gateway identifier.
- A carrier gateway should be created when one does not exist for the VPC, and cleaned up on deletion.
- Public IPv4 subnets in Wavelength Zones should route traffic through the carrier gateway rather than an internet gateway.
- IPv6 subnets in Wavelength Zones are not supported and should produce a clear error.
- Private subnets in Wavelength Zones should route traffic through the NAT gateway of their parent availability zone.

## Why This Matters

Without this support, users who want to run Kubernetes workloads in AWS Wavelength Zones — for use cases like low-latency mobile applications — cannot properly configure cluster networking. This change allows full subnet lifecycle management and correct routing for Wavelength Zone subnets.
