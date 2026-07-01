## Description

GraphQL servers are vulnerable to denial-of-service attacks through specially crafted introspection queries that are excessively large, deeply nested, or that repeat the same introspection field using different aliases. Currently the library has limited protection: it detects some obvious repeated introspection requests but misses alias-based repetition, and it has no way to hard-stop deeply nested or oversized queries before they consume significant server resources.

## Expected Behavior

- When building an executable representation of a GraphQL operation, it should be possible to configure a maximum number of fields allowed. If a query exceeds that limit, the processing should be aborted with a clear error message indicating the actual count versus the limit.
- The result of building an operation should expose metrics about the total number of fields and the maximum nesting depth encountered, so that defensive tooling can observe and act on them.
- The library should expose well-known public constants for the maximum field count and maximum depth it considers acceptable for a good-faith introspection query.
- The bad-faith detection for introspection should also flag queries that repeat the same introspection field using multiple aliases — not just unaliased repetitions.
- Deeply nested introspection queries (beyond a reasonable threshold) should be hard-stopped during execution rather than allowed to run to completion.

## Why This Matters

Without these protections, a malicious client can craft an introspection query with thousands of fields or very deep nesting to exhaust server resources. Adding configurable field-count limits, depth tracking, and alias-aware bad-faith detection closes these attack vectors and gives operators the observability they need to tune defenses appropriately.
