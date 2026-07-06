## Description

When fetching paginated lists of resources through the API client, the requested page size is not being forwarded to the server as a query parameter. As a result, the server falls back to its own internal default page size, which may differ from what the client expects. This mismatch causes offset calculations in subsequent paginated requests to go wrong, potentially producing duplicate entries or missing items in the final result.

## Expected Behavior

- When a caller specifies a page size (limit), that limit should be sent to the server as a query parameter on **every** HTTP request, including the initial request and all subsequent paginated requests.
- Without this, the server uses its own default page size, which can be different from the client's requested size, leading to incorrect pagination arithmetic.

## Current Behavior

The limit value is accepted as a parameter but is not included in the query parameters sent to the server. All paginated requests go out without the limit, so the server decides how many items to return independently.

## Why This Matters

Users relying on the CLI to list resources with a specific page size will get unreliable results — items may appear multiple times or not at all depending on how the server's default page size compares to the requested limit.
