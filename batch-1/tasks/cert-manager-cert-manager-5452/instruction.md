Update the Azure DNS provider in cert-manager to use the modern Azure SDK for authentication, removing support for the deprecated library and the retired Azure German Cloud environment. Implement validation for the tenant ID at initialization and ensure the provider supports workload identity with automatic token refresh.

*   Modify `NewDNSProviderCredentials` function:
    *   Accept `tenantID` as the fifth positional string parameter.
    *   Validate `environment` parameter to accept only "", "AzurePublicCloud", "AzureChinaCloud", and "AzureUSGovernmentCloud". Return an error for any other value, including "AzureGermanCloud".
    *   Validate `tenantID` to ensure it does not contain invalid characters such as spaces. Return an error if invalid.

*   Update `getAuthorization` function:
    *   Change the first parameter to `policy.ClientOptions` instead of `azure.Environment`.
    *   Remove `subscriptionID` from the function signature.
    *   Return `azcore.TokenCredential` as the primary return type.
    *   When workload identity is indicated (via `AZURE_FEDERATED_TOKEN_FILE` and empty `clientID`), return a `*azidentity.WorkloadIdentityCredential`.
    *   Ensure the returned credential implements `GetToken(ctx context.Context, options policy.TokenRequestOptions) (azcore.AccessToken, error)`.
    *   Implement automatic token refresh by re-exchanging the federated token upon expiry.
    *   Lowercase the `tenantID` in token endpoint URIs.
    *   Override the `client_id` in token requests with `managedIdentity.ClientID` if it is non-empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.