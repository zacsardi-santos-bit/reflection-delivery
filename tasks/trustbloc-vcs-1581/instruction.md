Update the OpenID credential issuer's well-known configuration endpoint to include missing fields necessary for wallets to properly understand and display supported credential types. Implement changes to both the internal profile data structures and the API response types, ensuring that all new fields are populated correctly.

*   Modify `CredentialConfigurationsSupported` struct in `pkg/restapi/v1/issuer/openapi.gen.go`:
    *   Add `Claims` field of type `*map[string]interface{}` with JSON tag `json:"claims,omitempty"`.
    *   Add `Doctype` field of type `*string` with JSON tag `json:"doctype,omitempty"`.
    *   Add `Order` field of type `*[]string` with JSON tag `json:"order,omitempty"`.
    *   Add `Vct` field of type `*string` with JSON tag `json:"vct,omitempty"`.
    *   Ensure `Scope` field is populated from the profile's credential configuration.

*   Modify `CredentialConfigurationsSupportedDefinition` struct in `pkg/restapi/v1/issuer/openapi.gen.go`:
    *   Add `Context` field of type `*[]string` with JSON tag `json:"@context,omitempty"`.

*   Update `CredentialsConfigurationSupported` struct in `pkg/profile/api.go`:
    *   Add `Claims` field of type `map[string]interface{}` with JSON tag `json:"claims"`.
    *   Add `Doctype` field of type `string` with JSON tag `json:"doctype"`.
    *   Add `Order` field of type `[]string` with JSON tag `json:"order"`.
    *   Add `Scope` field of type `string` with JSON tag `json:"scope"`.
    *   Add `Vct` field of type `string` with JSON tag `json:"vct"`.

*   Update `CredentialConfigurationsSupportedDefinition` struct in `pkg/profile/api.go`:
    *   Add `Context` field of type `[]string` with JSON tag `json:"@context"`.

*   Modify `buildCredentialConfigurationsSupported` method in `pkg/service/wellknown/provider/wellknown_service.go`:
    *   Map new fields `Claims`, `Doctype`, `Order`, `Scope`, and `Vct` using pointer wrappers.

*   Modify `buildCredentialDefinition` method in `pkg/service/wellknown/provider/wellknown_service.go`:
    *   Include `Context` field from the profile's credential definition, wrapped as a pointer.

*   Update test data in `pkg/service/wellknown/provider/testdata/profile.json`:
    *   Add `"claims": {"org.iso.18013.5.1.aamva": {"organ_donor": {}}}`.
    *   Add `"doctype": "doctype1"`.
    *   Add `"order": ["claimName1", "claimName2", "claimName3"]`.
    *   Add `"scope": "VerifiedEmployeeCredential"`.
    *   Add `"vct": "vct1"`.
    *   Under `"credential_definition"`, add `"@context": ["https://example.com/context/1"]`.

*   Ensure the `GetOpenIDCredentialIssuerConfig` service method includes all new fields populated from the issuer profile's credential metadata configuration.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.