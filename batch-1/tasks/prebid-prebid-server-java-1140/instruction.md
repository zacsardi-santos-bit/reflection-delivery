Update the Pulsepoint bidder adapter to support multiple ad formats beyond just banners. Ensure that the adapter accepts impressions of any media type (banner, video, audio, native) and correctly identifies the bid type in responses. Remove the ad size requirement and relax validation for publisher and tag IDs.

*   Modify the `ExtImpPulsepoint` model:
    *   Retain only `publisherId` (JSON key 'cp', Integer) and `tagId` (JSON key 'ct', Integer).
    *   Remove the `adSize` field (JSON key 'cf').
    *   Ensure the factory method accepts exactly two arguments: `Integer cp, Integer ct`.

*   Update the `PulsepointBidder` class:
    *   In `makeHttpRequests`:
        *   Accept all impression types without error.
        *   Do not validate publisher ID or tag ID as required or non-zero.
        *   Set the impression `tagid` to the string representation of the `tagId` value.
        *   Use the first valid publisher ID (non-null, >0) across impressions; if none, use an empty string.
        *   For the site object, create or update the `Publisher` with the resolved publisher ID.
        *   For the app object, create or update the `Publisher` with the resolved publisher ID.
    *   In `makeBids`:
        *   Match each bid's `impid` against request impressions.
        *   Drop bids with no matching impression or with a matched impression having no recognized media type.
        *   Determine bid type based on the matched impression's media type:
            *   `BidType.banner` for banner.
            *   `BidType.video` for video.
            *   `BidType.audio` for audio.
            *   `BidType.xNative` for native.

*   Ensure the `OpenrtbBidder` class:
    *   Filters out bids for which `getBidType` returns null, dropping unresolvable bid types.

*   Integration test requirements:
    *   Ensure the request fixture for Pulsepoint does not include the 'cf' field.
    *   Ensure the expected outgoing bid request fixture omits 'cf' from all impression extensions.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.