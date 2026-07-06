Consolidate IP range validation logic into a shared package to ensure consistent error messages across IPAM and external IP pool controllers. Implement new validation functions and update existing methods to use this centralized logic, ensuring clear and consistent error reporting.

*   Create a new package at `pkg/controller/validation` for shared IP range validation utilities.
*   Define `NormalizedIPRange` struct in `pkg/controller/validation/ippool.go`:
    *   Fields: `Start` (netip.Addr), `End` (netip.Addr), `Origin` (string).
*   Implement `GetIPRangeSet` function:
    *   Accepts a slice of `IPRange` objects and returns a `sets.Set[string]`.
    *   Represent each range as a CIDR string or 'start-end' format.
*   Implement `parseIPRangeCIDR` function:
    *   Returns error with message 'invalid cidr <cidrStr>' for invalid CIDR strings.
*   Implement `parseIPRangeStartEnd` function:
    *   Validates start IP first, then end IP, returning respective error messages.
*   Implement `validateIPRange` function:
    *   Validates start-end IP range with specific error messages for IP family mismatch and start greater than end.
*   Implement `normalizeRange` function:
    *   Sets `Origin` field based on CIDR or start-end format, including context if provided.
*   Implement `NormalizeRanges` function:
    *   Normalizes each IP range in a slice, returning `NormalizedIPRange` values or the first error.
*   Implement `ValidateIPRangesAndSubnetInfo` function:
    *   Validates IP ranges against optional subnet info, returning specific error messages for various validation failures.
*   Implement `RangesOverlap` function:
    *   Determines if two IP ranges overlap, considering IP family and touching endpoints.
*   Update `validateIPRangesAndSubnetInfoForExternalIPPool` function in `externalippool` package:
    *   Accepts `ExternalIPPool` pointer and slice of existing pools, returning overlap errors with specific pool references.
*   Update `ValidateIPPool` method on IPAM controller:
    *   Use updated error message formats and ensure existing IP ranges cannot be updated or deleted.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.