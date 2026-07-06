## Description

The Ghost admin dashboard needs to display top content analytics — which posts and pages are receiving the most visits. Currently there is no service to fetch this traffic data from the external analytics provider and match it up with meaningful content titles. Raw visit data contains URL pathnames but not the human-readable titles that would make the dashboard useful.

## Expected Behavior

A new service should:
- Fetch top content visit data from the external analytics provider
- Enrich each entry with its content title by looking up posts in the database and other resource types (tags, author pages, custom pages) via the URL routing layer
- Fall back to a human-readable label derived from the pathname when a page cannot be identified, and use "Home" as the label for the root URL
- Always return a safe, iterable response structure even when the analytics provider is unavailable or returns an error

A reusable utility for communicating with the analytics provider should also be introduced. It should:
- Build properly formatted API request URLs including standard query parameters (date range, timezone, membership status filter)
- Support a versioned pipe name variant when a version is specified
- Use a local development endpoint and token when local mode is configured, and ignore versioning in that mode
- Parse various response formats (JSON string, parsed object, nested body) into a consistent data array
- Return null instead of throwing when requests fail or responses cannot be parsed

## Why This Matters

Without this, the dashboard cannot display meaningful top-content statistics. Developers need a reliable, error-tolerant data layer that bridges raw analytics events with the Ghost content model.
