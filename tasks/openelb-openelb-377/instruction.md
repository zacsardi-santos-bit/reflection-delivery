Implement namespace-scoping support for IP pools in the OpenELB IP address management system. Ensure that IP pools can be restricted to specific namespaces and selected based on priority. Remove unnecessary protocol fields from allocation records and fix the IP release mechanism when services are deleted.

*   Update the `EipSpec` struct in `api/v1alpha2/eip_types.go`:
    *   Add `Namespaces []string` to specify namespace restrictions.
    *   Add `NamespaceSelector map[string]string` to match namespaces using labels.
    *   Add `Priority int` to determine selection preference, with lower values preferred.

*   Modify the `svcRecord` struct in `pkg/controllers/ipam/ipam.go`:
    *   Ensure it contains only `Key`, `Eip`, and `IP` fields.
    *   Remove the `Protocol` field completely.

*   Adjust the `Contains` method in `api/v1alpha2/eip_types.go`:
    *   Ensure it returns `false` for empty or nil `net.IP` values.

*   Implement changes in the `constructAllocate` function in `pkg/controllers/ipam/ipam.go`:
    *   Return a non-nil result with a release record when a service with an existing IP allocation is deleted.
    *   Automatically select an EIP based on the service's namespace using `Namespaces`, `NamespaceSelector`, and `Priority`.
    *   Select the EIP with the lowest `Priority` when multiple EIPs match.
    *   Use a default EIP pool marked with `OpenELBEIPAnnotationDefaultPool` as a fallback.

*   Update the `assignIPFromEip` function in `pkg/controllers/ipam/ipam.go`:
    *   Remove protocol-based filtering; do not reject IP assignments based on protocol mismatches.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.