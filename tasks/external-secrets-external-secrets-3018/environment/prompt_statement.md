I'm trying to use external-secrets with a Vault instance that requires clients to present a certificate when connecting. The Vault provider currently only supports configuring the CA certificate for server verification, but there's no way to configure a client certificate and private key. I'd like to be able to store the client certificate and key in a Kubernetes secret and reference them from the Vault store configuration, similar to how CA certificates are already configured.

The store validation should also enforce that both the certificate reference and the key reference are provided together — if only one is given, validation should fail. When the provider initializes and loads those credentials, if the key material is in an invalid format, a meaningful error should be returned.

Additionally, when the provider fails to look up a Kubernetes secret during initialization, the error message should include the secret's namespace so it's easier to diagnose the problem.
