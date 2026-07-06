# Missing Credential Configuration Fields in OpenID Issuer Well-Known Endpoint

## Description

The OpenID credential issuer's well-known configuration endpoint is missing several important fields that wallets need to properly understand and display supported credential types. When a wallet queries the issuer's configuration, it receives an incomplete response that omits:

- Claims metadata (structured list of attributes a credential can contain), required for certain mobile document and selective-disclosure credential formats
- Context URLs for the credential definition, required for linked data proof credentials
- Display ordering hints for credential claims
- Document type identifier (required for mobile document format credentials)
- Verifiable credential type designator (required for selective-disclosure format credentials)
- The scope value for the credential (currently always empty/absent)

## Expected Behavior

- The credential configurations supported section of the well-known response should include fields for claims metadata, document type, verifiable credential type, and claim ordering
- The credential definition within each credential configuration should include context URLs
- The scope field should be populated from the profile configuration rather than always being absent
- All new fields should be read from the issuer profile configuration and reflected in the API response

## Why This Matters

Without these fields, wallets cannot properly handle certain credential formats, cannot display credential claims in the intended order, and cannot correctly identify credential types. This prevents full compliance with the relevant OpenID for Verifiable Credential Issuance specification sections that describe these metadata fields.
