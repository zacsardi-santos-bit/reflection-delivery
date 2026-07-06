## Description

The SDK's model classes for audit log responses and user directory (SCIM) responses silently discard any API response fields that they don't explicitly know about. As a result, when Slack's API adds new fields to its responses, developers using the SDK have no way to access those new fields without waiting for an SDK update.

## Expected Behavior

- Audit log response objects — including the top-level response, individual log entries, and response metadata — should preserve any unrecognized fields from the API in a dedicated attribute so developers can access them directly.
- User directory (SCIM) response objects should likewise expose unrecognized fields via a dedicated attribute, with keys normalized to the standard Python naming convention (snake_case).
- When converting a user directory object back to a dictionary (e.g. to serialize it), the unknown fields should be included with their original API naming convention (camelCase) restored.

## Why This Matters

APIs evolve over time and regularly add new fields. An SDK that silently drops unknown fields forces developers to wait for SDK updates before they can use new API capabilities. By preserving unknown fields in an accessible attribute, the SDK becomes forward-compatible and developers can immediately work with new API data even if the SDK hasn't been formally updated to model those fields.

## Additional Note

For user directory (SCIM) objects, the existing named field for capturing extra attributes should be renamed for consistency with the new approach across the SDK.
