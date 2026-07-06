## Description

The Cyberint threat intelligence feed integration currently only supports bulk daily feed retrieval. However, analysts often need to investigate a single specific indicator — a URL, an IP address, a domain, or a file hash — without having to pull down the entire daily feed. We should add individual indicator lookup commands so analysts can query a single indicator and get enrichment details back directly.

Additionally, the API endpoint paths used for the daily feed need to be updated to reflect the correct routing structure used by the service. The current paths do not match the actual API paths, causing requests to fail. The date format passed to the API also needs to be corrected to use a date-only value rather than a full timestamp.

Finally, the display header translation logic should be reorganized: the existing function should be renamed to reflect that it handles feed-level indicators, and a new separate function should be added to translate the richer set of fields returned by individual indicator lookups — including WHOIS registration details, network metadata, and file attributes.

## Expected Behavior

- Analysts can query a single URL, IP address, domain, or file hash to retrieve threat intelligence data for that specific indicator.
- When an invalid value is provided (malformed URL, non-IPv4 address, non-conforming domain, or invalid hash format), the command rejects the input with a descriptive validation error.
- The daily feed endpoint uses the correct URL path and date format.
- The integration's main dispatcher routes each new command to the appropriate handler.
- Individual indicator detail fields are mapped to human-readable column headers in result tables.

## Why This Matters

Without individual indicator lookup, analysts must process the entire daily feed to investigate one specific observable. Adding targeted lookup commands significantly reduces the effort required for single-indicator investigation and enables more efficient triage workflows.
