## Description

The Pulsepoint bidder adapter currently only supports banner ads and rejects any impression that does not specify a banner. It also requires an explicit ad size parameter as part of the bidder extension, and it enforces strict validation on publisher ID and tag ID. These constraints prevent Pulsepoint from participating in auctions for video, audio, and native ad inventory, and the required ad size field creates unnecessary friction.

## Expected Behavior

- The Pulsepoint adapter should accept impressions of any media type — banner, video, audio, and native — rather than rejecting non-banner impressions with an error.
- When returning bids, the adapter should identify the correct bid type (banner, video, audio, or native) by inspecting the media type present in the matching impression from the request, rather than always returning banner.
- Bids whose impression ID does not match any impression in the request, or whose matched impression has no recognized media type, should be silently dropped.
- The ad size parameter should be removed from the required bidder extension fields; only the publisher ID and tag ID should be required.
- When determining the publisher ID to use for the outgoing request, the first valid publisher ID across all impressions should be used rather than the last one. If no valid publisher ID is found, an empty string should be used instead of returning an error.
- Publisher ID and tag ID validation should not cause the request to fail; missing or zero values should be handled gracefully.

## Why This Matters

Restricting Pulsepoint to banner-only inventory means the adapter cannot compete in video, audio, or native auctions. Removing the ad size requirement and relaxing validation makes the adapter more robust and aligned with the actual capabilities of the Pulsepoint exchange.
