## Description

The Palo Alto Networks Security Advisories integration fetches security advisory data from the Palo Alto Networks API and converts it into threat intelligence indicators. The upstream API has been updated to use a new version of their advisory data schema, which reorganizes where key information is located within the response. Fields such as the CVE identifier, advisory title, description, publication date, severity scores, vulnerability classification references, and external links are now nested at different paths compared to the previous schema.

As a result, the integration is no longer able to correctly parse advisory responses. Indicators produced by the integration end up with missing or incorrect field values — the wrong CVE identifier, incorrect severity, missing description, and malformed publication references.

## Expected Behavior

- The integration correctly reads the CVE identifier from its new location in the advisory response
- Advisory titles, descriptions, publication dates, and severity information are extracted from their updated locations in the response schema
- Vulnerability classification tags (CWE identifiers) are extracted as a flat list of IDs from the new nested structure
- Publication references are extracted with only their URL links (no title or source fields that no longer exist in the new schema)
- The test data file that drives integration verification is updated to reflect the new advisory schema format and the correct expected output

## Why This Matters

Security teams rely on this integration to automatically ingest Palo Alto Networks security advisories as up-to-date threat intelligence indicators. With the API schema change, the integration silently produces incorrect indicators, leading analysts to work with inaccurate or incomplete vulnerability data. Updating the parsing logic ensures the integration remains functional after the upstream API update.
