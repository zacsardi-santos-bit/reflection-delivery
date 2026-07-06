# Update Analytics Tracking to Use Ghost-Operated Proxy Endpoint

## Description

The analytics tracking script currently sends visitor events directly to the third-party analytics provider's infrastructure using an older data source identifier. We need to update this to route traffic through Ghost's own proxy endpoint, and rename the data source to a simpler identifier. This is part of migrating analytics infrastructure to a Ghost-controlled layer.

Additionally, there is no way for developers to test analytics locally without their traffic hitting the production endpoint. We need a local development mode that lets developers redirect analytics tracking to a locally running proxy, using a separate token and endpoint, so they can develop and debug without polluting production data.

## Expected Behavior

- The tracker script injected into page heads must point to the new Ghost-operated proxy endpoint rather than the old third-party host
- The data source name used by the tracker must be updated to the simpler identifier
- A new optional local mode must be supported: when enabled via configuration, all analytics traffic is redirected to a local proxy endpoint using the local credentials
- When local mode is disabled (the default), the tracker uses the production endpoint and credentials as before
- The analytics tracker script must read its host and authentication token from its own data attributes rather than requiring them to be configured elsewhere

## Analytics API Test Fixtures

A suite of test fixtures for the analytics reporting APIs should be added to verify that query pipelines return correctly aggregated data. These fixtures cover:

- Key performance indicator metrics per date (visits, pageviews, bounce rate, average session duration)
- Top browsers, devices, locations, operating systems, pages, and traffic sources by visit count
- Support for filtering by browser, device, location, OS, page path, referral source, and member subscription status
- Correct timezone-aware date boundary handling
- Single-day date ranges that return hourly breakdowns across the full 24-hour period

## Why This Matters

Routing analytics through a Ghost-controlled proxy gives Ghost more control over data handling and enables future improvements to the analytics pipeline. The local development mode makes it much easier to work on analytics features without risk of affecting production metrics.
