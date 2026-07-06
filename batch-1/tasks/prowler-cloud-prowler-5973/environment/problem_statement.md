## Description

When working with cloud security findings in the Prowler API, there is currently no efficient way to get a summary of which cloud services and regions are affected by findings on a specific date or with specific filters. Users and dashboards have to fetch and process all findings individually to determine the scope of impacted services and regions, which is slow and wasteful.

## Expected Behavior

- A new endpoint should allow querying for the distinct set of services and regions impacted by findings, optionally filtered by date and severity.
- When called with a valid date filter, the endpoint should return the distinct services and regions from the matching findings.
- When additional filters such as severity are applied, the returned services and regions should reflect only the findings that match those filters.
- When no findings match the given filters (for example, for a date in the future), the endpoint should return empty lists for both services and regions.
- When an invalid date format is provided as a filter value, the endpoint should reject the request with a clear validation error rather than silently ignoring the bad input.

## Why This Matters

Dashboards and reporting tools need a quick way to understand the scope of cloud services and regions affected by security findings without paging through the entire findings list. This endpoint makes it possible to efficiently summarize impact by date and severity.
