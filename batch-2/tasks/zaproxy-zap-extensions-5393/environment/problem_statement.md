## Description

The automation add-on's API endpoint for retrieving plan progress does not correctly expose all the information it should. The current response format has two main problems: timestamps are not formatted as standardized date strings, and the info, warning, and error message lists are not serialized as simple string arrays — instead they are being wrapped in a nested structure that is difficult for clients to parse.

## Expected Behavior

- The plan progress response should include a plan ID, start time, finish time, and three message lists (info, warnings, errors).
- Start and finish timestamps must be formatted as standard ISO 8601 UTC strings, not as raw date objects.
- In JSON, the info, warning, and error fields must be plain arrays of strings, not nested objects.
- In XML, the info, warning, and error fields must each be serialized as a list element containing individual message child elements, with each list element clearly typed as a list.
- When a timestamp is absent, the corresponding field should be an empty string rather than being omitted.

## Why This Matters

Clients relying on this endpoint cannot reliably parse the current response because dates and message lists are not in a consistent, standard format. Fixing the serialization makes the plan progress data properly accessible via both the JSON and XML APIs.

> **Note:** This is a breaking change — existing clients will need to update their parsing logic to handle the new format.
