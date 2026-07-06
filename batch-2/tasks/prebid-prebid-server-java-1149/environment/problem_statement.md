## Description

The server currently stores per-bidder site, app, and user configuration overrides inside a "first party data" section within each bidder config block. However, the OpenRTB 2.x protocol standard uses a dedicated section keyed as "ortb2" to carry these top-level request objects for individual bidders. Our current implementation is inconsistent with the spec, which creates confusion and interoperability issues for integrators.

## Expected Behavior

- Bidder-specific site, app, and user overrides should be stored under an ortb2-keyed section within each bidder configuration block, following the OpenRTB 2.x protocol structure.
- When a request includes both the legacy context-and-user format and the new ortb2 format for the same bidder, the server should automatically merge them before processing. For site data, the legacy context field should be merged into the ortb2 site object (with the legacy format taking priority for conflicting fields). For user data, the legacy user field should similarly be merged into the ortb2 user object.
- The app object should NOT be merged between the legacy and new formats — it should remain separate.
- After merging, the original legacy fields should be left untouched.
- The normalization of OpenRTB field type inconsistencies within bidder configs should operate on the new ortb2 section.

## Why This Matters

Aligning the bidder configuration structure with the OpenRTB 2.x standard makes integration more predictable and correct for bidders relying on the protocol. Providing automatic merging between the legacy format and the new format ensures backward compatibility during the transition period without losing any data from either source.
