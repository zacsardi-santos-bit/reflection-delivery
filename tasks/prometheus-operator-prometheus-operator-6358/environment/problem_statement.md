## Description

The web server TLS configuration for Prometheus operator components currently only supports providing TLS certificates, private keys, and client CA certificates via Kubernetes Secrets or ConfigMaps. This is limiting for users who mount TLS materials directly as files into the container — for example, via external certificate managers that write cert files to the filesystem. There is currently no way to point the web server TLS configuration at a file path directly.

Additionally, the current validation logic has a gap: it requires a certificate and a key to be supplied via Kubernetes resources, but the error messages and validation order do not clearly cover all combinations of valid and invalid configurations.

## Expected Behavior

- Operators should be able to specify the TLS certificate, private key, and client CA certificate as plain file paths on the container filesystem, in addition to the existing Kubernetes Secret and ConfigMap references.
- File-path fields and Kubernetes resource reference fields for the same TLS material should be mutually exclusive — specifying both should produce a validation error.
- The web server TLS configuration must always require both a certificate and a key to be present (via either a file path or a Kubernetes reference). Providing a client CA without both a cert and a key should result in a validation error.
- When file paths are configured, the generated TLS server configuration should reference those file paths directly.

## Why This Matters

Users who use tools that write TLS certificates directly to the filesystem (rather than into Kubernetes Secrets) cannot currently configure the Prometheus web server to use HTTPS without duplicating credentials into a Kubernetes Secret. Supporting file path references removes this friction and aligns the web TLS configuration with standard Prometheus configuration patterns.
