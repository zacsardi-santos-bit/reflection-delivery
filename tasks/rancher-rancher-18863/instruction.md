Implement functionality to inspect X.509 certificate trust chains by parsing raw certificate data into objects and determining their signing relationships. Ensure the system can differentiate between certificates signed by a recognized authority and self-signed certificates.

*   Implement the function `loadCertsFromPEM(certsByte []byte) ([]*x509.Certificate, error)` in the `pkg/controllers/user/logging/utils/testwrap.go` file.
    *   Parse PEM-encoded certificate data from `certsByte` and return a slice of `x509.Certificate` pointers.
    *   Return a non-empty slice for valid PEM-encoded certificates, including self-signed and CA-chain certificates.
    *   Return an error if the certificate bytes are malformed or cannot be parsed.
    *   Return an empty slice (not an error) if no CERTIFICATE blocks are found in the PEM input.

*   Implement the function `isSignedBy(cert, rootCA *x509.Certificate) bool` in the `pkg/controllers/user/logging/utils/testwrap.go` file.
    *   Return `true` if `cert` was signed by `rootCA`.
    *   Ensure the function returns `true` when a self-signed certificate is checked against its own issuing CA.
    *   Return `true` when a certificate signed by a third-party CA is checked against that CA.
    *   Return `false` when a certificate is checked against a CA that did not sign it.

*   Both `loadCertsFromPEM` and `isSignedBy` should be unexported functions within the `utils` package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.