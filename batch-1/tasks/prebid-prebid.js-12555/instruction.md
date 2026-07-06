Implement an analytics adapter for the R2B2 advertising platform within Prebid.js to collect and report auction performance data. Ensure the adapter captures key auction lifecycle events and sends them in a structured, batched format to a configurable server endpoint.

*   Update `modules/r2b2AnalyticsAdapter.js` to:
    *   Provide a default export of the adapter object, registered under provider code 'r2b2'.
    *   Include a named export function `resetAnalyticAdapter()` to reset all internal adapter state.
    *   Implement the `enableAnalytics(config)`, `disableAnalytics()`, and `track({eventType, args})` methods in the `r2b2Analytics` object.
*   Ensure `enableAnalytics` checks for a 'domain' field in options:
    *   Log a warning: "R2B2 Analytics: Mandatory parameter 'domain' not configured, analytics disabled" and disable the adapter if missing.
*   Implement the `track` method to handle Prebid events:
    *   Capture events: AUCTION_INIT, BID_REQUESTED, BID_RESPONSE, AUCTION_END, SET_TARGETING, BID_WON.
    *   Serialize events into a JSON payload with structure `{prebid: {e: [...]}}`, where each entry includes 'e' (event name) and 'd' (event data).
*   Send analytics data via an ajax call after a short delay:
    *   Use POST with body starting 'events=' followed by the JSON payload.
    *   Include URL query parameters: 'hbDomain', 'conf', 'conf_ver' for events; 'd', 'conf', 'conf_ver' for errors.
*   Handle specific event requirements:
    *   AUCTION_INIT: Emit 'init' entries with auctionId and ad unit details.
    *   BID_REQUESTED: Emit 'request' entries mapping ad units to 1.
    *   NO_BID: Emit 'noBid' entries with auctionId, bidderCode, and adUnitCode.
    *   BID_TIMEOUT: Count timeouts per bidder and ad unit, emit a single 'timeout' entry.
    *   BIDDER_DONE: Emit 'bidderDone' entries with auctionId and bidderCode.
    *   AUCTION_END: Emit 'auction' entries with detailed auction data, omit if no bidder requests.
    *   BID_RESPONSE: Emit 'response' entries with bid details.
    *   BID_REJECTED: Emit 'reject' entries with rejection details.
    *   BID_WON: Emit 'bidWon' entries with bid and targeting data.
    *   SET_TARGETING: Emit 'targeting' entries with ad unit targeting data.
    *   AD_RENDER_SUCCEEDED: Emit 'render' entries with render success details.
    *   AD_RENDER_FAILED: Emit 'renderFail' entries with failure reason.
    *   STALE_RENDER: Emit 'staleRender' entries with stale render details.
    *   BID_VIEWABLE: Emit 'view' entries with viewability timing.
*   Report errors for bid events without prior AUCTION_INIT:
    *   Send error reports with 'm' parameter containing "No auction data when creating event".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.