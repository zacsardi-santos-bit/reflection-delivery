## Description

We need to add a new pipeline step for integrating Contrast Security scan results into the CI/CD pipeline. Currently, there is no way for teams using this library to fetch vulnerability data from Contrast Security and incorporate it into their automated security gates.

## Expected Behavior

- The new step should accept configuration for Contrast Security credentials (API key, username, service key) and application identifiers (server URL, organization ID, application ID).
- Configuration should be validated upfront: all required fields must be non-empty. If the server URL is missing the secure protocol prefix, the step should normalize it automatically.
- The step should be able to authenticate against the Contrast Security API by producing the correct encoded credential string from the username and service key.
- It should construct the correct API and user-interface URLs for a given application based on the server, organization, and application identifiers.
- Vulnerability findings should be fetched with full pagination support, retrieving all pages of results automatically.
- Findings should be classified by severity into two tiers: high-priority (covering critical, high, and medium severity) and lower-priority (covering low and informational severity). Within each tier, a finding should be counted as reviewed if its status is anything other than newly reported.
- A structured tool record should be produced for the scanned application, capturing the application name, URL, and identifier, and returning an error when the application identifier is missing.

## Why This Matters

Security teams need automated pipeline enforcement for Contrast Security scan results. Without this step, teams must manually check scan results outside the pipeline. This integration allows vulnerability thresholds to be enforced automatically as part of CI/CD, reducing the window between vulnerability discovery and remediation.
