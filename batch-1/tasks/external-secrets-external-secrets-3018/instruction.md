Implement support for mutual TLS in the Vault provider by allowing users to specify client-side TLS credentials stored in Kubernetes secrets. Ensure the Vault client uses these credentials for authentication when connecting to Vault.

*   Update the `VaultProvider` struct:
    *   Add a `ClientTLS` field of type `VaultClientTLS` in `apis/externalsecrets/v1beta1/secretstore_vault_types.go`.
    *   Ensure `VaultClientTLS` includes:
        *   `CertSecretRef *SecretKeySelector` for the client certificate.
        *   `KeySecretRef *SecretKeySelector` for the client private key.
*   Implement validation in `ValidateStore` function in `pkg/provider/vault/vault.go`:
    *   Return an error if only one of `CertSecretRef` or `KeySecretRef` is provided.
    *   Return no error if both are provided and valid, or if neither is provided.
*   Modify Vault client initialization:
    *   Retrieve certificate and key from Kubernetes secrets if both `ClientTLS.CertSecretRef` and `ClientTLS.KeySecretRef` are set.
    *   Use 'tls.crt' and 'tls.key' as default keys in the secrets.
    *   Return an error using `errClientTLSAuth` if the private key data is not valid PEM-encoded data.
    *   Ensure successful initialization if the certificate and private key are valid.
*   Update error handling:
    *   Use `errGetKubeSecret` format string to include secret name, namespace, and underlying error when a Kubernetes secret lookup fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.