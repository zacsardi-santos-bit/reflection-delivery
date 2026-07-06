Implement changes to the proxy access authorization system to support multiple allowed proxy principals and verify certificate-bound access tokens against these principals. Update the format to accept a list and ensure the system checks the certificate's identity against this list.

*   Update the `extractX509CertSpiffeUri` method in the `Crypto` utility class:
    *   Return `null` if the certificate has no Subject Alternative Name URIs.
    *   Return `null` if the certificate has URIs but none start with "spiffe://".
    *   Return the URI string if the certificate contains exactly one SPIFFE URI.
    *   Return `null` if the certificate contains more than one SPIFFE URI.
    *   Return `null` for certificates with only non-SPIFFE URIs.

*   Modify the AccessToken construction process:
    *   Ensure that if the token's authorization details include a `proxy_access` entry with a principal field as a non-empty JSON array, and the certificate's SPIFFE URI matches any entry (case-insensitively), the construction succeeds.
    *   Ensure construction succeeds if the SPIFFE URI matches any principal in a multi-principal array.
    *   Fail construction by throwing a `CryptoException` with 'Confirmation failure' if:
        *   Authorization details are null or absent.
        *   Authorization details JSON is malformed or unparseable.
        *   The principal field is not a JSON array.
        *   The principal field is an empty JSON array.
        *   The certificate's SPIFFE URI does not match any principal in the array.

*   Update the token issuance endpoint:
    *   Ensure the `proxy_access` authorization details format specifies the principal field as a JSON array of strings.
    *   Allow requests using this array format to succeed and return a valid response.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.