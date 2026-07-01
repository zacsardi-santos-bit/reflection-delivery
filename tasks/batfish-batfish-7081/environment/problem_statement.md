## Description

Batfish currently cannot parse or analyze BGP route aggregation configurations from Cisco NX-OS devices. When a network engineer configures BGP aggregate addresses on NX-OS — which combine multiple more-specific routes into a single summary advertisement — those configuration statements are not understood by Batfish's NX-OS parser and are not converted into the normalized data model used for cross-vendor analysis.

## Expected Behavior

- The NX-OS parser should recognize BGP aggregate address statements and their optional modifiers, including: AS-path inheritance, route-map-based filtering of contributing routes, route-map-based attribute transformation, suppression of more-specific routes, and route-map-based selective suppression.
- Parsed aggregate configurations should be stored in the vendor-specific model with all modifier settings preserved.
- During conversion to the normalized model, aggregate entries should be converted appropriately, with suppression policy applied when routes are configured to suppress all more-specific prefixes, and attribute-map transformations preserved.
- Undefined route-map references in aggregate configurations should be treated as absent (equivalent to the option not being configured).
- Unsupported track object types that previously generated placeholder objects and parse warnings should now be silently ignored instead.

## Why This Matters

Without this support, Batfish silently ignores BGP aggregation behavior in NX-OS configs, potentially producing incomplete or incorrect analysis for networks that rely on route summarization. Once implemented, analysts can reason about how NX-OS BGP aggregation affects route advertisement and suppression.
