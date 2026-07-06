Implement support for parsing and analyzing BGP route aggregation configurations in Cisco NX-OS devices within Batfish. Ensure that aggregate address statements and their modifiers are recognized and stored in both the vendor-specific and normalized data models.

*   Update the NX-OS BGP configuration parser to recognize the aggregate-address statement in IPv4 unicast address families, including optional modifiers: `as-set`, `advertise-map <name>`, `attribute-map <name>`, `summary-only`, and `suppress-map <name>`.
    *   Ensure combinations of these modifiers are supported on a single aggregate.
*   Store parsed aggregate address configurations in `BgpVrfIpv4AddressFamilyConfiguration.getAggregateNetworks()` as a `Map<Prefix, BgpVrfAddressFamilyAggregateNetworkConfiguration>`, keyed by the aggregate prefix.
*   Construct `BgpVrfAddressFamilyAggregateNetworkConfiguration` with parameters: `String advertiseMap`, `boolean asSet`, `String attributeMap`, `boolean summaryOnly`, `String suppressMap`.
    *   Implement `equals()` for value-based comparison using all five fields.
*   Store route-map names verbatim in aggregate-address statements, regardless of definition status. Undefined route-map names should remain as-is.
*   During conversion to the normalized data model:
    *   Create a `BgpAggregate` for each aggregate address entry using `BgpAggregate.of(Prefix prefix, String suppressionPolicyName, String generationPolicyName, String attributeMapName)`.
    *   Set `suppressionPolicyName` to `SUMMARY_ONLY_SUPPRESSION_POLICY_NAME` when `summary-only` is set.
    *   Set `suppressionPolicyName` to null when `summary-only` is not set and a `suppress-map` is configured.
    *   Pass the attribute-map name as `attributeMapName` in `BgpAggregate.of()`. Use null if no attribute-map is configured.
    *   Use null for `generationPolicyName` as `as-set` and `advertise-map` handling is not yet implemented.
*   Treat undefined route-map references as absent during conversion, resulting in null values for corresponding policy name fields in `BgpAggregate`.
*   Silently ignore unsupported track object types during parsing, avoiding placeholder objects and parse warnings.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.