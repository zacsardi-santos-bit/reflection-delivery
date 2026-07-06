## Description

When the CLI fetches organizations or spaces by their unique identifiers, it currently always sends those identifiers as a server-side filter query to the Cloud Controller API. However, older versions of the Cloud Controller API do not support this filter parameter. Sending it to an older server can cause incorrect or unexpected results.

## Expected Behavior

- When connected to a Cloud Controller API that is new enough to support identifier-based filtering, the CLI should send the identifiers as a server-side filter query (efficient path).
- When connected to an older Cloud Controller API that does not support this filter, the CLI should omit the filter entirely, retrieve all records, and filter them locally to return only the records matching the requested identifiers.
- If the API version string returned by the server is malformed or cannot be parsed, the CLI should safely default to the client-side filtering approach rather than failing with an error.
- In all cases, the returned results must contain only the records matching the requested identifiers.

## Why This Matters

Operators running the CLI against older Cloud Controller deployments can encounter subtle bugs because the CLI assumes server capabilities that don't exist. By detecting the API version at runtime and adapting accordingly, the CLI becomes compatible with a wider range of Cloud Controller versions without sacrificing efficiency on modern deployments.
