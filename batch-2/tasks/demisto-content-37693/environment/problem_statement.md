## Description

The Azure AD Connect Health feed integration is returning duplicate indicators when scraping endpoint data from Microsoft's documentation. When the feed fetches the list of network endpoints that agents need to connect to, the same endpoint can appear more than once in the result, leading to redundant indicator entries.

## Expected Behavior

- Each unique URL endpoint should appear exactly once in the returned indicators.
- Each unique wildcard domain endpoint (domain glob) should appear exactly once in the returned indicators.
- The feed should not return extra or repeated entries for any indicator type.

## Current Behavior

The feed scraper returns more indicators than expected — the same URL or domain glob pattern can appear multiple times. This happens because the scraper is matching content from HTML elements that contain repeated or nested data, rather than from the canonical element where each endpoint is listed exactly once.

## Why This Matters

Duplicate indicators create noise in downstream threat intelligence pipelines and may cause unnecessary processing or false duplicate detection in SIEM/SOAR platforms that consume this feed. The feed should provide clean, deduplicated data to consumers.
