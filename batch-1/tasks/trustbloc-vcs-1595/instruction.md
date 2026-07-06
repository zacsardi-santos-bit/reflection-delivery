Implement support for scope-based credential requests in the OIDC4VCI authorization code flow. Update the system to handle requests using OAuth scopes instead of requiring authorization details, ensuring compliance with the OpenID for Verifiable Credential Issuance specification.

*   Update the `PushAuthorizationDetails` function:
    *   Translate `ErrInvalidCredentialConfigurationID` into a validation error with the message 'invalid credential configuration ID', targeting 'authorization_details.credential_configuration_id'.

*   Modify the `PrepareAuthorizationRequest` controller endpoint:
    *   Accept requests with a 'scope' array field alongside or instead of 'authorization_details'.
    *   Forward scope values to the service layer in `PrepareClaimDataAuthorizationRequest` struct's Scope field.
    *   Accept scope-only requests by setting `AuthorizationDetails` to nil in the service call.

*   Adjust the `OidcAuthorize` controller handler:
    *   Do not return an error when `authorization_details` is not supplied.
    *   Forward the request with `AuthorizationDetails` set to nil and Scope set to the OAuth-requested scopes.

*   Implement the `checkScopes` function:
    *   Accept a transaction pointer, requested scopes, and a boolean for authorization details presence.
    *   Validate scopes against the transaction's allowed scopes if authorization details are supplied.
    *   Perform profile-based scope validation when authorization details are not supplied.
    *   Fetch issuer profile using transaction's ProfileID and ProfileVersion for scope validation.
    *   Return `resterr.ProfileNotFound` if the profile is not found.
    *   Return a `SystemError` for non-'not found' profile fetching errors.
    *   Return `resterr.ErrInvalidScope` if issuer credential metadata is nil or no configuration matches requested scopes.
    *   Return `resterr.ErrCredentialFormatNotSupported` if matched credential configuration's format is incorrect.
    *   Return `resterr.ErrCredentialTypeNotSupported` if matched credential configuration's type is incorrect.
    *   Ignore duplicate and unknown scopes, returning only valid matched scopes.

*   Update `PrepareClaimDataAuthorizationResponse`:
    *   Set the Scope field to valid scopes returned by `checkScopes`.
    *   Ensure the transaction store `Update` is called once per request.

*   Enhance `checkTransactionAuthorizationDetails`:
    *   Return a validation error if authorization details lack both credential format and configuration ID.

*   Revise issuer profile metadata:
    *   Use distinct credential configuration identifiers in `CredentialsConfigurationSupported` map.
    *   Include a Scope field for scope-based credential matching.

*   Update BDD step functions:
    *   Modify `runOIDC4VCIAuthWithCredentialConfigurationID` to use credentialConfigurationID instead of credential type name.
    *   Implement `runOIDC4VCIAuthWithScopes` for scope-based credential issuance, registered for the pattern 'User interacts with Wallet to initiate credential issuance using authorization code flow with scopes "<scopes>"'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.