Implement service discovery for Hetzner Cloud and Robot platforms in VictoriaMetrics. Parse JSON API responses to generate Prometheus-compatible metadata labels for automatic server discovery and monitoring.

*   Implement `parseHcloudNetworksList` in `lib/promscrape/discovery/hetzner/hcloud.go`:
    *   Parse Hetzner Cloud Networks JSON API response from `data []byte`.
    *   Return a pointer to `HcloudNetworksList` with `Networks` populated from the JSON "networks" array.

*   Implement `parseHcloudServerList` in `lib/promscrape/discovery/hetzner/hcloud.go`:
    *   Parse Hetzner Cloud Servers JSON API response from `data []byte`.
    *   Return a pointer to `HcloudServerList` with all fields of `HcloudServer` correctly populated.

*   Implement `appendTargetLabels` method on `HcloudServer` in `lib/promscrape/discovery/hetzner/hcloud.go`:
    *   Append a label set to the provided slice `ms` using the server details and return the extended slice.
    *   Include labels for server identity, public and private IPs, datacenter, image, and server type details.

*   Implement `parseRobotServersList` in `lib/promscrape/discovery/hetzner/robot.go`:
    *   Parse Hetzner Robot Servers JSON array response from `data []byte`.
    *   Return a pointer to `robotServersList` with `Servers` populated from the JSON array.

*   Implement `appendTargetLabels` method on `RobotServerResponse` in `lib/promscrape/discovery/hetzner/robot.go`:
    *   Append a label set to the provided slice `ms` using the server details and return the extended slice.
    *   Include labels for server identity, public IPs, datacenter, product, and cancellation status.

*   Ensure JSON field mappings:
    *   Map `PrivateNet.ID` to JSON field "network".
    *   Map `RobotServer.Canceled` to JSON field "cancelled".
    *   Handle `RobotServer.Subnet` as `nil` if JSON array is null.

*   Organize code:
    *   Place Hetzner Cloud-related types and functions in `hcloud.go`.
    *   Place Hetzner Robot-related types and functions in `robot.go`.
    *   Ensure both files reside in `lib/promscrape/discovery/hetzner/`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.