Implement a region filter and IPv6 range metadata label in Linode service discovery for Prometheus. Ensure that only instances from a specified region are returned and that IPv6 delegated prefix ranges are included as metadata labels.

*   Update the `SDConfig` struct in `discovery/linode/linode.go`:
    *   Add a `Region` string field with the YAML tag `region,omitempty`.
    *   Initialize `Region` to an empty string in `DefaultSDConfig`.

*   Implement region filtering:
    *   When `Region` is specified in `SDConfig`, return only instances from that region during the refresh operation.
    *   When `Region` is empty, return instances from all regions.
    *   Apply the region filter consistently across all API calls during refresh, including instances, IP addresses, and IPv6 ranges.

*   Add IPv6 range metadata:
    *   Include a `__meta_linode_ipv6_ranges` label for instances with routed IPv6 ranges.
    *   Format the label value as a comma-separated list of CIDR ranges, enclosed by commas (e.g., `,2600:3c04:e001:456::/64,`).
    *   Fetch IPv6 range data from the `/v4/networking/ipv6/ranges` API endpoint.
    *   Match IPv6 ranges to instances by comparing the `route_target` field with the instance's primary IPv6 SLAAC address (strip `/128` suffix).

*   Ensure the label format for each IPv6 range is `range/prefix` (e.g., `2600:3c04:e001:456::/64`).

*   Create test fixture files under `discovery/linode/testdata/`:
    *   Subdirectories: `no_region_filter`, `us-east`, and `ca-central`.
    *   Each subdirectory must contain:
        *   `v4/account/events.json` (empty events list).
        *   `v4/linode/instances.json` (instances specific to the region).
        *   `v4/networking/ips.json` (IP address data consistent with instances).
        *   `v4/networking/ipv6/ranges.json` (IPv6 range data consistent with instances).

*   Ensure testdata consistency:
    *   `no_region_filter` must include all 4 test instances (IDs 26838044, 26848419, 26837938, 26837992).
    *   `us-east` must include instances 26838044 and 26837992.
    *   `ca-central` must include instance 26837938.
    *   IP and IPv6 range data must match the instances in each directory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.