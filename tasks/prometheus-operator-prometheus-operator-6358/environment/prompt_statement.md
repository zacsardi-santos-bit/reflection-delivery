I'm working with the prometheus-operator project and I'd like to extend the web server TLS configuration to support specifying certificates, keys, and client CA certificates as file paths on the container filesystem, in addition to the existing support for Kubernetes Secrets and ConfigMaps.

Right now, if I mount a TLS certificate into the Prometheus container as a file (for example, using an external certificate manager that writes cert files to disk), I have no way to tell the web server TLS configuration to use those files. Everything must go through Kubernetes Secrets or ConfigMaps, which means I'd have to duplicate my credentials.

I'd like the TLS configuration type to accept optional file path fields for the certificate, key, and client CA. A file path field and its corresponding Kubernetes resource reference field should be mutually exclusive — specifying both should be rejected with a validation error. The validation should also require that both a certificate and a key are always present (via either a file path or a Kubernetes resource); a configuration that has a client CA but is missing a cert or a key should not be valid.

When a file path is provided for a TLS material, the generated server configuration output should reference that file path directly, rather than constructing a path from a mounted secret or configmap.
