## Description

We need two new automation scripts for case management, plus an extension to our core integration to support retrieving cases.

**Search Cases:** There is currently no dedicated script to search for cases using time-based filters. Analysts need to be able to look up cases by creation time range using flexible date inputs (ISO timestamps, relative expressions like "1 day ago", or absolute dates). The script should translate human-readable time inputs into the internal time filter format the API expects. If an end time is specified without a start time, the script should return a clear error indicating that a start time is required.

**Get Case Extra Data:** There is no single automated way to fetch the full context for a specific case — including its linked sub-issues, network artifacts, and file artifacts — in one operation. A new script should retrieve all this information and return it in a structured, consistent format. When sub-issues or artifact data is absent from the API response, those fields should gracefully default to empty or null values rather than failing.

**Core Integration — Get Cases Command:** The core integration lacks a dedicated command for retrieving cases. A new command should wrap the existing incident-fetching capability and normalize the response field names for external consumers (mapping internal API terminology to the user-facing terms "case" and "issue"). The command must enforce a maximum result limit to prevent excessive queries, validate that at least one filter is provided before querying, and detect conflicting time filter arguments.

## Expected Behavior

- Searching by time range maps human-readable date inputs to the correct internal filter fields.
- Providing an end time without a start time produces a descriptive error.
- Fetching case extra data returns a flat dict with the case fields plus extracted sub-issue IDs, network artifacts, and file artifacts.
- Missing fields in the case extra data response default to empty list (for sub-issue IDs) or null (for artifact collections).
- The core integration's case-retrieval command normalizes field names (translating internal terminology to user-facing terms) and caps the result count at 100.
- Calling the case-retrieval command without any filter parameters raises a descriptive error.

## Why This Matters

These additions enable analysts to automate case investigation workflows without writing custom logic to handle API quirks and field name inconsistencies.
