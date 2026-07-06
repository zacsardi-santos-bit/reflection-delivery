Implement the `BuildPrecertTBS` function to correctly handle the construction of "to-be-signed" certificate data from a pre-certificate, accommodating various scenarios involving the presence or absence of Authority Key Identifier (AKI) extensions.

Requirements:

*   Implement `BuildPrecertTBS` in `x509/x509.go` with the signature:
    ```go
    BuildPrecertTBS(rawTBSCert []byte, preIssuer *Certificate) ([]byte, error)
    ```
    *   Accepts raw DER-encoded TBSCertificate bytes and an optional pre-issuer Certificate pointer.
    *   Returns modified DER-encoded TBSCertificate bytes and an error.

*   Handle AKI extension scenarios:
    *   If `preIssuer` is `nil`, return a valid TBS with the original AKI preserved from the input pre-certificate's TBS.
    *   If both the pre-certificate TBS and `preIssuer` have an AKI, update the AKI in the returned TBS to the `preIssuer`'s AKI.
    *   If the pre-certificate has an AKI but `preIssuer` does not, remove the AKI from the returned TBS.
    *   If the pre-certificate has no AKI and `preIssuer` has an AKI, set the AKI in the returned TBS to the `preIssuer`'s AKI.
    *   If neither has an AKI, return a TBS with no AKI extension.

*   Modify the TBS data:
    *   Replace the Issuer field in the TBS with `preIssuer.RawIssuer` if `preIssuer` is provided and valid.
    *   Remove the CT poison extension identified by `OIDExtensionCTPoison` from the returned TBS certificate data.

*   Error handling:
    *   Return an error if the provided `preIssuer` Certificate does not include `ExtKeyUsageCertificateTransparency` in its extended key usages.

*   Ensure the returned bytes:
    *   Are valid, parseable ASN.1 DER-encoded TBSCertificate data with no trailing bytes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.