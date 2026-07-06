## Description

The cert-manager Venafi integration currently depends on an older major version of the Venafi certificate management client library. A newer major version has been released that includes bug fixes, security improvements, and continued vendor support. The project should be updated to use the newer major version so that it benefits from those improvements and remains on a supported dependency.

## Expected Behavior

- All code in the Venafi issuer and controller packages should use the newer major version of the Venafi client library.
- Certificate requests to Venafi (both Trust Protection Platform and Venafi Cloud) should continue to work correctly after the upgrade.
- Certificate retrieval via pickup IDs should continue to work correctly after the upgrade.
- Configuration building for both TPP (username/password and access token auth) and Cloud (API key auth) issuers should continue to work correctly.
- The module dependency files should reflect the updated library version.

## Why This Matters

Staying on older major versions of actively-maintained dependencies creates technical debt, may expose the project to unfixed bugs, and can block adoption of improvements that only appear in newer versions. Upgrading to the current major version ensures continued compatibility and access to vendor support.
