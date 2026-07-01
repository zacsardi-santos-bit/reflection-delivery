## Add ManageEngine CloudDNS Provider

## Description

The lego ACME client library supports many DNS providers for automating DNS-01 certificate challenges, but ManageEngine CloudDNS is currently not among them. Users who manage their DNS through ManageEngine CloudDNS have no way to automate certificate issuance and renewal with lego today.

This issue requests adding a new DNS provider integration for ManageEngine CloudDNS so that users can supply their client credentials via environment variables and have lego automatically create and clean up the DNS TXT records required for ACME certificate challenges.

## Expected Behavior

- Users can configure the provider by setting two environment variables: one for the client ID and one for the client secret.
- If either credential is missing when the provider is initialized from environment variables, a clear error message should list the names of the missing variables.
- If credentials are explicitly missing when constructing the provider from a configuration object, a concise error should be returned indicating credentials are missing.
- The provider's internal HTTP client must communicate with the ManageEngine CloudDNS REST API to list zones, list SPF/TXT records within a zone, and create, update, or delete individual records.
- API errors returned from the server should be surfaced to callers, conveying both the HTTP status code and the descriptive error message from the response body.

## Why This Matters

ManageEngine CloudDNS users currently have no automated path to obtain certificates through lego. Adding this provider closes that gap and enables fully automated certificate management for ManageEngine-managed domains.
