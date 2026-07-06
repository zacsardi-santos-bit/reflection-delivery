Implement a new data type to represent the "ortb2" bidder configuration and update the bidder configuration structure to align with the OpenRTB 2.x protocol standard. Ensure that the system can handle both the legacy "first party data" format and the new "ortb2" format, merging them appropriately while maintaining backward compatibility.

*   Create the `ExtBidderConfigOrtb` class in `src/main/java/org/prebid/server/proto/openrtb/ext/request/ExtBidderConfigOrtb.java`.
    *   Implement a static factory method `of(ObjectNode site, ObjectNode app, ObjectNode user)` that returns an `ExtBidderConfigOrtb` instance.
    *   Ensure the class is a value type, with equality based on field values.

*   Update the `ExtBidderConfig` class in `src/main/java/org/prebid/server/proto/openrtb/ext/request/ExtBidderConfig.java`.
    *   Add a new field of type `ExtBidderConfigOrtb` named `ortb2`.
    *   Update the static factory method to accept `ExtBidderConfigFpd` and `ExtBidderConfigOrtb` parameters: `of(ExtBidderConfigFpd fpd, ExtBidderConfigOrtb ortb2)`.

*   Modify the `FpdResolver` class in `src/main/java/org/prebid/server/auction/FpdResolver.java`.
    *   Update the `resolveBidRequestExt` method to create bidder configs using `ExtBidderConfig.of(null, ExtBidderConfigOrtb.of(siteNode, null, userNode))`.

*   Update the `OrtbTypesResolver` class in `src/main/java/org/prebid/server/auction/OrtbTypesResolver.java`.
    *   Modify the `normalizeBidRequest` method to process bidder configs from `config.ortb2` instead of `config.fpd`.
    *   Implement merging of `config.fpd.context` into `config.ortb2.site` and `config.fpd.user` into `config.ortb2.user`, with legacy data taking priority in conflicts.
    *   Ensure `config.fpd.app` is not merged into `config.ortb2.app`.

*   Ensure that after merging, the original `config.fpd` fields remain unmodified.

*   Implement the `JsonMerger.merge()` method to correctly merge two `ExtBidderConfigOrtb` objects, combining site and app fields and returning a new `ExtBidderConfigOrtb` with the merged values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.