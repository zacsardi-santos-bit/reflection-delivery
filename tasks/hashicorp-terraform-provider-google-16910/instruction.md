Resolve the persistent diff issue in `google_compute_instance_group_manager` and `google_compute_region_instance_group_manager` resources by implementing functions to preserve the order of stateful IP configurations as specified in Terraform. Ensure that the functions handle discrepancies between the configuration and the API response correctly.

Requirements:

*   Implement `flattenStatefulPolicyStatefulExternalIps` and `flattenStatefulPolicyStatefulInternalIps` functions.
    *   Each function must accept:
        *   `*schema.ResourceData` as the first parameter to read the current configuration state.
        *   `*compute.StatefulPolicy` as the second parameter to access the API state.
    *   Each function must return a `[]map[string]interface{}` where each map contains:
        *   `interface_name` (string)
        *   `delete_rule` (string, sourced from the AutoDelete field of the API IP object).
    *   Preserve the order of IPs as specified in the Terraform configuration:
        *   IPs present in the configuration must appear first in the result, maintaining the same order.
        *   IPs returned by the API but not present in the configuration must be appended at the end, sorted alphabetically by `interface_name`.
        *   IPs present in the configuration but not returned by the API must be omitted from the result.
    *   If the configuration contains no IPs but the API returns some, the result must include all API IPs, sorted alphabetically by `interface_name`.
    *   If both the configuration and the API contain no IPs, return an empty slice (not nil).

*   Update the `ResourceComputeRegionInstanceGroupManager` resource schema:
    *   Define `stateful_external_ip` and `stateful_internal_ip` as list attributes.
    *   Each list attribute must include `interface_name` and `delete_rule` sub-fields to enable reading config ordering via the `*schema.ResourceData` parameter.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.