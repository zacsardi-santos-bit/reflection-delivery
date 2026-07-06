## Description

The TCF (Transparency and Consent Framework) privacy metrics emitted during auction processing are inaccurate in several ways, leading to misleading data in our monitoring dashboards.

## Issues Found

1. **Swapped metric flags**: The parameters for "analytics blocked" and "request blocked" in the auction TCF metric recording function are in the wrong order, causing these two metrics to be incremented for the wrong conditions.

2. **Missing per-version request count metric**: There is no metric tracking how many TCF consent requests were processed per TCF version. A new counter should be incremented for each valid consent request, broken down by TCF version.

3. **Inflated masking metrics when request is fully blocked**: When a bid request is blocked entirely by privacy enforcement, the system should only record "request blocked" — not user ID removal or geo masking, since those are irrelevant once the whole request is blocked. Currently, other metrics are still being incremented alongside the block.

4. **Geo and user ID masking counted when there is no data to mask**: The user-ID-removed and geo-masked metrics are being incremented even when the bidder's user object has no actual private user identifiers, and when neither the user nor device has any geographic data. These metrics should only fire when there was actual sensitive data present that got masked.

5. **TCF metrics fired for invalid consent strings**: The per-version request count and geo metrics are being emitted even when the consent string is empty or otherwise invalid. Metrics should only be recorded for valid, non-empty consent strings.

## Expected Behavior

- When a request is fully blocked, only the "request blocked" metric should fire; user ID removal, geo masking, and analytics blocking metrics should remain zero.
- The analytics blocked and request blocked counters should be mapped to the correct corresponding conditions.
- A new counter should track TCF request counts per version.
- User ID removal and geo masking metrics should only fire when there is actual private data to mask.
- Per-version TCF metrics should only be emitted for valid (non-empty) consent strings.

## Why This Matters

Inaccurate privacy metrics make it impossible to correctly assess how often each type of privacy enforcement action is actually taken, leading to incorrect compliance reporting.
