## Description

The PIR (Personal Information Removal) scan feature sends telemetry events to help the team understand scan performance and device behavior. Two issues need to be addressed:

1. **Manufacturer normalization**: The device manufacturer name is currently sent as-is in pixel events. This means the same manufacturer can appear with different casing depending on how the device reports it, and uncommon/obscure brands add noise to aggregated analytics. Manufacturer values should be normalized to lowercase, and only well-known brands should be reported directly — any brand not on the recognized list should be grouped under a generic "other" category.

2. **Missing scan context in telemetry**: When reporting scan completion and initial scan duration events, several useful fields are missing: the number of data brokers involved in the scan, the number of profile queries used, and whether the device's power-saving mode was active at scan time. Without these fields, it's difficult to correlate scan performance with device conditions and workload.

## Expected Behavior

- Manufacturer values in pixel requests are always lowercased
- Known device brands are reported as their lowercased name; unknown brands are reported as "other"
- Scan completion events include profile query count, broker count, and power-saving status
- Initial scan duration events include power-saving status, battery optimization status, and broker count

## Why This Matters

These fields help the team understand whether scan performance issues correlate with specific manufacturers, large broker/profile counts, or power-saving constraints. Without normalization, manufacturer data is unreliable for grouping and analysis.
