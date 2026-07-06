## Description

The threat intelligence feed integration for Unit 42 ATOMs data fails to correctly parse indicators from reports that have a nested hierarchy. Top-level reports reference sub-reports, which in turn contain the actual threat indicators. Currently, some sub-reports that should be processed and included as output indicators are being skipped, and the relationships generated within parsed reports can carry invalid indicator types that are not recognized by the platform.

## Expected Behavior

- Both top-level ("main") reports and their referenced sub-reports should be produced as output indicators — not just the top-level ones.
- The system should correctly classify which reports are main reports (those whose references consist entirely of intrusion-sets and other reports) versus sub-reports (those that directly contain indicator and other non-report references).
- Relationship entries generated for reports must only use entity types that are valid and recognized by the platform's indicator type mapping.
- The logic for extracting an attack technique identifier and value from a name string (splitting at the colon separator) should be centralized in the shared TAXII2 API module rather than duplicated in the feed integration.
- When fetching a specific report object by ID returns multiple matches, a debug message should be recorded and the ambiguous result should be skipped.

## Why This Matters

When the indicator fetch command is run against a Unit 42 ATOMs feed, the expected number of indicators is not returned because sub-reports are incorrectly classified and skipped. This means threat intelligence data visible in the raw feed is silently dropped, leaving analysts with an incomplete picture. Additionally, any relationships that carry unsupported indicator types can cause downstream processing errors.
