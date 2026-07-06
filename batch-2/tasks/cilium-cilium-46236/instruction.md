I'm working on the network driver that handles pod resource claims and static IP address annotations.

*   When the driver has IPv4 disabled (ipv4Enabled=false), the device setup configuration produced by prepareResourceClaim must have an invalid (zero) IPv4 address, even if the pod's static IP annotation provides an IPv4 address.

*   When the driver has IPv6 disabled (ipv6Enabled=false), the device setup configuration produced by prepareResourceClaim must have an invalid (zero) IPv6 address, even if the pod's static IP annotation provides an IPv6 address.

*   When the driver has both IPv4 and IPv6 enabled (dual-stack) and the annotation provides both addresses, both the IPv4 and IPv6 addresses from the annotation must be applied to the device setup configuration.

*   When the driver has only IPv4 enabled and the annotation provides both IPv4 and IPv6 addresses, only the IPv4 address from the annotation must be applied; the IPv6 address must be absent (invalid) in the device setup configuration.

*   When the driver has only IPv6 enabled and the annotation provides both IPv4 and IPv6 addresses, only the IPv6 address from the annotation must be applied; the IPv4 address must be absent (invalid) in the device setup configuration.

*   When the driver has both IP families enabled and the annotation provides only an IPv4 address (no IPv6), the IPv4 from the annotation must be used and the IPv6 must fall back to the claim's default configuration.

*   When the driver has both IP families enabled and the annotation provides only an IPv6 address (no IPv4), the IPv6 from the annotation must be used and the IPv4 must fall back to the claim's default configuration.


*   Interface details: Type: Struct Field
Name: ipv4Enabled
Location: pkg/networkdriver/ (Driver struct)
Signature: ipv4Enabled bool
Description: Boolean field on the Driver struct indicating whether IPv4 address family is enabled. When false, IPv4 addresses must not be applied to device setup configurations, even if present in pod annotations.

Type: Struct Field
Name: ipv6Enabled
Location: pkg/networkdriver/ (Driver struct)
Signature: ipv6Enabled bool
Description: Boolean field on the Driver struct indicating whether IPv6 address family is enabled. When false, IPv6 addresses must not be applied to device setup configurations, even if present in pod annotations.

Type: Method
Name: prepareResourceClaim
Location: pkg/networkdriver/
Signature: prepareResourceClaim(ctx context.Context, claim *resourceapi.ResourceClaim) prepareResult
Description: Processes a resource claim for a pod, applying static IP addresses from annotations to device setup configurations. Must respect ipv4Enabled and ipv6Enabled on the driver — only addresses for enabled IP families should be included in the resulting setup configuration (setupCfg.IPv4Addr and setupCfg.IPv6Addr).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.