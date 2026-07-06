## Description

The server's first-party data (FPD) handling for impression-level context needs to be redesigned to support two separate data locations: contextual data nested under the impression's context object, and top-level impression data. Currently, the inline auction logic manually strips contextual data for bidders that don't have FPD permission, but this logic is fragile and not composable.

We need a dedicated resolver method that correctly handles merging contextual data and impression-level data when a bidder has FPD access, and strips or omits that data when a bidder does not. The resolver should produce a deep copy of data nodes rather than sharing references, and it should handle non-object context or data gracefully (e.g., leaving arrays unchanged rather than crashing or corrupting them).

## Expected Behavior

- When FPD is permitted for a bidder, contextual data (from the context's nested data sub-object) and top-level impression data should be merged into the result's data field. If both are present as objects, their fields should be combined.
- When FPD is not permitted, the data sub-field should be removed from context, and no top-level data field should be included in the result.
- If removing the data sub-field from context leaves the context empty, the context field itself should be omitted entirely.
- Non-object values (such as arrays) for context or data fields should be tolerated and passed through unchanged rather than causing errors.
- When user-level data in a bid request is not an object (e.g., it is provided as an array), the normalization step should skip processing it rather than treating it incorrectly.

## Rubicon Adapter Ad Slot Priority

The Rubicon adapter reads ad slot information from impression data to populate targeting fields. The priority order for picking the ad unit code should be updated to check multiple data sources in a defined sequence:

1. GAM adserver slot from the contextual data adserver entry (when adserver name is "gam")
2. GAM adserver slot from the impression-level data adserver entry (when adserver name is "gam")
3. Prebid ad slot field from contextual data
4. Prebid ad slot field from impression-level data

Non-GAM adserver entries should not produce an ad unit code.

## Why This Matters

These changes allow the server to properly support the standard structure where contextual and impression-level first-party data are kept in distinct locations, while giving the auction logic a clean way to decide which data each bidder is allowed to receive based on their permissions.
