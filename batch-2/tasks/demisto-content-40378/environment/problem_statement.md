## Description

The ServiceNow event collector integration supports fetching audit logs and syslog transactions, but it has no support for collecting customer service case events. Cases represent a distinct category of activity in ServiceNow and are served by a different API endpoint than the other two log types. Security teams that monitor ServiceNow need case data alongside audit and syslog information to have a complete picture.

Beyond just adding a new data source, the current code handles log type identity through scattered string constants and separate lookup dictionaries, which makes it error-prone to add new types and hard to maintain. Each log type has its own run-state keys, API path, and display name, but none of that is grouped together — it's spread across multiple constants and conditional blocks.

## Expected Behavior

- A third event type — customer service cases — should be fetchable in the same way as audit logs and syslog transactions.
- All per-type metadata (API path construction, run-state dictionary keys, string identifiers used in collected events) should be centralized in a single structured representation rather than separate constants.
- Helper functions for enriching events, deduplicating events, determining the fetch start time, and converting user-facing type names to internal types should accept this structured representation instead of raw strings.
- The client should resolve the correct API URL and default fetch limit per log type using the structured representation.
- Fetching case events on demand via a dedicated command should work the same way as for the other two event types.

## Why This Matters

Without this change, analysts cannot collect ServiceNow case data through the integration, leaving a gap in their monitoring coverage. The refactor also makes it straightforward to add additional log types in the future without modifying multiple independent constant definitions and conditional branches.
