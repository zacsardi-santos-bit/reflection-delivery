## Description

When listing replays from the batch API, the source information displayed to users is incomplete and confusing. Currently, the output only shows a raw collection name (if one exists), but does not distinguish between replays sourced from a regular collection versus those sourced from a dead-letter stage. Additionally, the replay type is shown as-is from the API (e.g., lowercase), rather than being normalized to a more readable format.

## Expected Behavior

- When a replay is sourced from a collection, the source column should display a clearly labeled value such as "Collection - <name>" to make the source type explicit.
- When a replay is sourced from a dead-letter stage, the source column should display "Dead Letter Stage - <name>" so users can distinguish it from collection-based replays.
- The replay type should be presented in a normalized, human-readable form (e.g., title-cased) rather than the raw lowercase API value.
- The schema listing should not surface internal fields that are not meaningful to end users. Specifically, the protobuf root type field should be removed from the schema output.

## Why This Matters

Users viewing replay listings need to immediately understand where a replay is sourced from. Without a clear label, a dead-letter stage source looks indistinguishable from a missing or empty collection. Cleaning up the schema output also reduces confusion by removing a field that serves no practical purpose in the displayed results.
