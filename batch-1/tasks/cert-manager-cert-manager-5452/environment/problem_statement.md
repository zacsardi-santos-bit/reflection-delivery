## Description

The Azure DNS provider in cert-manager relies on an older Azure authentication library that is now deprecated. Microsoft has also retired the Azure German Cloud environment, which means that cloud option should no longer be supported. The provider should be updated to use the modern Azure SDK for authentication, which changes how credentials are obtained and how tokens are acquired and refreshed.

## Expected Behavior

- The provider should use the current Azure SDK authentication types rather than the deprecated library.
- The provider should no longer accept the German Cloud environment as a valid configuration option; attempting to use it should return an error.
- The provider should validate the tenant ID at initialization time, returning a clear error when the tenant ID is invalid (e.g., contains spaces or otherwise malformed values), rather than failing later at runtime.
- When using workload identity, the credential should automatically re-fetch a new token from the authorization server when the current token expires, by re-exchanging the federated token.
- The client ID specified through the managed identity configuration section should override the ambient client ID when requesting tokens.
- The tenant ID should appear lowercased in token endpoint URIs.

## Why This Matters

Using a deprecated authentication library creates maintenance burden and security risk. Removing support for a retired cloud environment avoids misleading configuration. Validating inputs like tenant ID eagerly means operators get actionable errors at startup instead of confusing failures when the controller tries to use Azure DNS.
