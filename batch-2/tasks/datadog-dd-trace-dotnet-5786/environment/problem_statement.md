# Add MySQL 9 Support to Integration Tests

## Description

The MySQL integration test suite only covers MySQL version 8, and the version-detection logic uses an approach that only matches that one specific major version. As MySQL 9 has become available, it is not included in the integration test matrix and would be silently misclassified as a "legacy" server, applying the wrong configuration path during testing.

## Expected Behavior

- MySQL version 9 (and any future major versions 8 or higher) should be included in the integration test package version lists for all supported .NET target frameworks.
- The helper that splits package versions into "new" and "old" groups should use a comparison against the major version number (8 or higher = new), not a string-prefix match or exact equality check against version 8.
- The test helper that was previously split into two separate methods (one for new MySQL, one for old) should be consolidated into a single unified method that accepts a boolean parameter indicating which group of versions to return.
- The integration test sample application should correctly recognize MySQL 9 as a current (non-legacy) server — not treat it the same as pre-version-8 servers.
- Supporting system packages used by the integration test sample application must be updated to versions compatible with the MySQL 9 driver package.
- A separate SQLite package version used in the test matrix should be bumped to the latest available patch release.

## Why This Matters

Without this fix, MySQL 9 integration tests would silently fall back to the legacy server path, producing incorrect tracing behavior and leaving a major MySQL release uncovered by automated testing. The version-detection fix also future-proofs the test logic so MySQL 10 and later will be correctly classified without further changes.
