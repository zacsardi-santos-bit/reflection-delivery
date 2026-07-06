## Description

When searching for issues in an experiment, it would be very useful to see how many traces are associated with each issue — so teams can quickly understand the impact of each issue without making additional API calls. Currently, the issue search result does not include any trace count information, making it difficult to triage and prioritize issues by their scope.

## Expected Behavior

- The issue search should support an optional flag that, when enabled, returns each issue with a count of the traces linked to it.
- When the flag is not set, the search behaves exactly as before (no trace count information is returned).
- When the flag is enabled, issues that have zero linked traces should return a count of 0, not a missing value.
- When the flag is enabled, results within the same severity level should be sorted by trace count in descending order, so the most-impacted issues appear first.
- The trace count option should work correctly alongside existing filters and pagination.

## Why This Matters

Teams often need to prioritize issues by the number of traces affected. Without trace count in the search response, users must make separate API calls or manual queries to determine which issues have the highest trace volume. Adding this capability directly to the issue search response makes issue triage faster and more efficient.
