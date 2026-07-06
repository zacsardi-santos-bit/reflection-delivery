Implement the necessary changes to correct the TCF privacy metrics in the prebid server. Ensure that metrics are accurately recorded according to the specified conditions and parameters.

*   Update the `updateAuctionTcfMetrics` method in `src/main/java/org/prebid/server/metric/Metrics.java`:
    *   Ensure parameters are in the following order: `String bidder`, `MetricName requestType`, `boolean userIdRemoved`, `boolean geoMasked`, `boolean analyticsBlocked`, `boolean requestBlocked`.
    *   Increment all four metric counters when all boolean flags are true: `adapter.{bidder}.{type}.tcf.userid_removed`, `adapter.{bidder}.{type}.tcf.geo_masked`, `adapter.{bidder}.{type}.tcf.analytics_blocked`, `adapter.{bidder}.{type}.tcf.request_blocked`.
    *   Set `userIdRemoved=false`, `geoMasked=false`, `analyticsBlocked=false`, and `requestBlocked=true` when `blockBidderRequest` is true, regardless of other flags.
    *   Set `analyticsBlocked=true` only when the analytics report is blocked and the bidder request is not fully blocked.
    *   Set `userIdRemoved=true` only when private user data is present and the enforcement action requires removing user IDs. Private user data includes non-null user id, non-null buyeruid, non-empty eids in the user extension, or non-null digitrust.
    *   Set `geoMasked=true` only when geo data is present (non-null `user.geo` or `device.geo`) and the enforcement action requires masking geo.

*   Add a new method `updatePrivacyTcfRequestsMetric` in `src/main/java/org/prebid/server/metric/Metrics.java`:
    *   Signature: `updatePrivacyTcfRequestsMetric(int version)`
    *   Increment the counter at `privacy.tcf.v{version}.requests` for the given TCF version.

*   Modify the TCF definer service:
    *   Call `updatePrivacyTcfRequestsMetric` with the TCF version number only when processing a valid (non-empty) consent string.
    *   Extract the TCF version from the consent string and use it in `updatePrivacyTcfRequestsMetric`.
    *   Ensure `updatePrivacyTcfGeoMetric` is not called when the consent string is empty or invalid.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.