## Description

Currently, the OIDC4VCI authorization code flow requires clients to always supply `authorization_details` when initiating credential issuance. If a wallet client omits `authorization_details` and relies only on OAuth scopes to indicate which credential type it wants, the system returns an error and rejects the request. This behavior is non-compliant with the OpenID for Verifiable Credential Issuance specification, which allows clients to request credentials using either authorization details or scopes.

## Expected Behavior

- A wallet client should be able to initiate the authorization code flow using only OAuth scopes (without authorization details) to request a specific credential type.
- The issuer service should validate the requested scopes against the issuer's credential configuration metadata, checking that the credential format and type match what is configured.
- Duplicate or unknown scopes in the request should be silently ignored; only valid matched scopes should be returned in the response.
- Issuer profile credential configuration identifiers should be distinct identifiers, decoupled from the credential type name itself.
- Each credential configuration entry in the issuer's metadata can define a scope value that is used to match incoming scope-based requests.
- If no credential configuration matches the requested scopes, or if the metadata is missing, the request must be rejected with an appropriate error.
- When authorization details are provided but are structurally incomplete (missing both credential format and configuration ID), the request must be rejected with a descriptive error.
- When the issuer profile cannot be found during scope validation, an appropriate profile-not-found error must be returned.

## Why This Matters

Supporting the scope-based credential request method makes the system compliant with the OpenID4VCI specification's alternative authorization path, enabling broader interoperability with wallet clients that prefer or require scope-based flows over the authorization_details-based approach.
