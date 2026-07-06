Update the credential issuance service to use a structured representation for supported credential types and configuration metadata. Implement changes to ensure optional fields are correctly handled and signed metadata is returned in a consistent JSON format.

*   Modify `CredentialMetaData` in `pkg/profile/api.go`:
    *   Change `CredentialsSupported` from `[]map[string]interface{}` to `[]*CredentialsSupported`.
    *   Define `CredentialsSupported` struct with fields: `ID`, `Format`, `Types`, `CredentialSubject`, and `Display`.

*   Update `WellKnownOpenIDIssuerConfiguration` in `pkg/restapi/v1/issuer/openapi.gen.go`:
    *   Change fields to pointer types: `CredentialIssuer`, `AuthorizationEndpoint`, `CredentialEndpoint`, `CredentialAckEndpoint`, `TokenEndpoint`, `ResponseTypesSupported`, `GrantTypesSupported`, `ScopesSupported`, `TokenEndpointAuthMethodsSupported`, `PreAuthorizedGrantAnonymousAccessSupported`.
    *   Add new optional fields: `DeferredCredentialEndpoint`, `NotificationEndpoint`, `CredentialResponseEncryption`, `CredentialIdentifiersSupported`, `SignedMetadata`, `CredentialConfigurationsSupported`.

*   Introduce `WellKnownOpenIDIssuerConfiguration_CredentialConfigurationsSupported`:
    *   Implement custom JSON marshal/unmarshal methods.
    *   Use `AdditionalProperties` map for JSON keys.

*   Create `CredentialConfigurationsSupported` struct:
    *   Fields: `Format`, `CryptographicBindingMethodsSupported`, `CryptographicSuitesSupported`, `ProofTypes`, `Display`, `CredentialDefinition`, `Scope`.

*   Define `CredentialConfigurationsSupportedDefinition` struct:
    *   Fields: `Type`, `CredentialSubject`.

*   Introduce `CredentialResponseEncryption` struct:
    *   Fields: `AlgValuesSupported`, `EncValuesSupported`, `EncryptionRequired`.

*   Modify `Logo` struct in `pkg/restapi/v1/issuer/openapi.gen.go`:
    *   Change `Url` field to `Uri` (required, non-pointer).

*   Update `GetOpenIDCredentialIssuerConfig` in `pkg/service/wellknown/provider/wellknown_service.go`:
    *   Populate `CredentialConfigurationsSupported` as a map keyed by credential type.
    *   Ensure fields are nil unless explicitly set.

*   Modify `GetOpenIDConfig` in `pkg/restapi/v1/issuer/controller.go`:
    *   Return pointer-typed fields for configuration.
    *   Set `RegistrationEndpoint` when `EnableDynamicClientRegistration` is true.

*   Update `OpenidCredentialIssuerConfig` in `pkg/restapi/v1/issuer/controller.go`:
    *   Return JSON response for signed metadata with `Content-Type` as `application/json; charset=UTF-8`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.