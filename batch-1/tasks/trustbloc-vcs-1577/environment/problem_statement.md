## Description

The credential issuer's published configuration currently uses a loosely-typed, unstructured representation for listing supported credential types. Each supported credential is stored as an arbitrary key-value map rather than a well-defined typed structure, which makes it impossible to reliably access specific fields (like the credential format) without unsafe type assertions. Additionally, multiple fields on the issuer configuration object are required non-nullable values even when they are not applicable to every issuer, preventing consumers from distinguishing between "not configured" and "explicitly false/empty."

There is also a problem with how signed issuer metadata is returned from the well-known endpoint: it is currently served as a raw token with a token-specific content type, rather than as a JSON document wrapping the signed value. This inconsistency complicates clients that expect a uniform JSON response format from the endpoint.

## Expected Behavior

- Supported credential types should be described using a proper typed structure with named fields (id, format, credential types, credential subject, display), not an untyped map.
- The credential configuration should expose a structured map of supported credential types, where each entry is keyed by the credential type name and includes cryptographic binding methods, cryptographic suites, proof types, display metadata, and credential definition details.
- Fields on the issuer configuration that may not always apply (authorization endpoints, token endpoints, grant types, scopes, pre-authorized access flags, etc.) should be optional, with absent values clearly distinguished from zero values.
- When signed issuer metadata is present, the well-known endpoint should return a JSON response containing the signed value, with an appropriate JSON content type.
- The registration endpoint should be included in the configuration when dynamic client registration is enabled for the issuer profile.

## Why This Matters

These changes align the issuer metadata with the current OpenID for Verifiable Credential Issuance specification, which defines a structured map of credential configurations rather than a flat array of untyped objects. Making configuration fields optional prevents misleading defaults and allows clients to correctly interpret absent values. The consistent JSON response format for signed metadata simplifies client integration.
