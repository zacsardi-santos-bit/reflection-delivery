## Description

The Qualys v2 integration can fetch vulnerability knowledge base data based on a modification date, but it currently has no way to fetch vulnerabilities targeted by specific identifiers associated with asset detections. This forces the integration to pull all recently-changed vulnerabilities even when only a small subset is relevant to the assets currently being processed.

In addition, there is no coordinated flow for fetching both assets and their associated vulnerabilities together and sending them to the XSIAM platform in a synchronized way. Assets and vulnerabilities need to be paired so that XSIAM can understand their relationship, and progress across multiple fetch cycles must be tracked with cumulative counts and snapshot identifiers.

## Expected Behavior

- The integration should support fetching vulnerability data either by date range or by a list of specific vulnerability identifiers.
- A validation error should be returned if neither a date nor a list of identifiers is provided when fetching vulnerabilities.
- A new fetch mode should coordinate fetching assets and vulnerabilities together, sending both to XSIAM with the correct vendor and product labels, cumulative counts, and a snapshot identifier.
- While a multi-page fetch cycle is still in progress, the reported item count should signal that more data is coming. When the cycle finishes, the actual cumulative count should be reported.
- After a complete fetch cycle ends, the integration state should reset to its default starting configuration automatically.
- The date used for fetching should be calculated as 90 days before the current date.

## Why This Matters

Without targeted vulnerability fetching, every sync pulls far more data than necessary, degrading performance and increasing costs. The new coordinated fetch flow enables XSIAM to receive a complete, consistent snapshot of assets and vulnerabilities together, with proper tracking across paginated results.
