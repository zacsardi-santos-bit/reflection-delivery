## Description

We need a new feed integration for the Google Threat Intelligence platform that automatically ingests curated threat indicator lists into our security operations environment. Currently, there is no way to pull these threat feeds into the platform, forcing analysts to manually track threat indicators from this source.

The integration should support fetching four types of threat indicators: files (identified by their cryptographic hashes), domains, URLs, and IP addresses. Each indicator type has distinct metadata that should be extracted and mapped to the platform's standard indicator fields — for example, domain indicators should include registration details parsed from WHOIS data, file indicators should include hash values and file properties, and IP indicators should include geographic information.

## Expected Behavior

- Indicators from the threat feed should be fetched and converted into platform-standard indicator objects with type, value, severity score, and enriched metadata fields.
- The threat verdict from the intelligence service should be mapped to a platform score: malicious maps to 3, benign maps to 1, suspicious maps to 2, and undetected or unknown verdicts map to 0.
- For domain indicators, admin, registrant, and registrar contact details should be extracted from WHOIS text.
- For URL indicators, the actual URL string (not an internal hash identifier) should be used as the indicator value.
- For IP indicators, the country code should be included as a standard field.
- Both on-demand retrieval (manual command) and automated periodic ingestion (feed fetch) should be supported.
- A connectivity test command should verify the integration is properly configured.

## Why This Matters

Security analysts rely on up-to-date threat intelligence feeds to detect and respond to threats. Without automated ingestion of these threat lists, analysts must manually track indicators, which is error-prone and slow. This integration closes that gap and ensures threat intelligence is always current in the platform.
