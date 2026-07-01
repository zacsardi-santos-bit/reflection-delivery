## Description

The Vault provider currently supports TLS for verifying the Vault server's identity, but it does not support mutual TLS — where the client also presents a certificate to authenticate itself to Vault. This means users who have Vault configured to require client certificates at the transport layer cannot use external-secrets with it.

We need to add support for specifying client-side TLS credentials (a certificate and a private key stored in a Kubernetes secret) in the Vault store configuration. The provider should use these credentials when establishing connections to Vault.

## Expected Behavior

- Users can configure a reference to a Kubernetes secret containing a client certificate and a client private key in the Vault store spec.
- Store validation must require that both the certificate reference and the key reference are provided together — supplying only one should be rejected.
- During Vault client initialization, the referenced Kubernetes secret(s) are fetched and the TLS credentials are loaded. If the key material is malformed, a clear error is returned.
- If valid credentials are found, the Vault client initializes successfully and uses the client certificate for all connections.
- When a Kubernetes secret lookup fails during initialization, the error message should include the namespace of the secret to improve debuggability.

## Why This Matters

Many organizations run Vault with mutual TLS enabled for an additional layer of security. Without client certificate support in the Vault provider, external-secrets cannot integrate with these Vault deployments.
