Implement TLS support for Fission's HTTP trigger ingress configuration. Allow users to specify a Kubernetes Secret for TLS termination when creating or updating routes. Ensure the ingress is automatically configured with the specified TLS secret and provide functionality to remove or preserve existing TLS configurations as needed.

*   Update the IngressConfig struct:
    *   Add a `TLS` field of type string with JSON key 'tls' to store the name of the Kubernetes Secret for TLS.

*   Modify the GetIngressConfig function:
    *   Update the signature to: `GetIngressConfig(annotations []string, rule string, tls string, fallbackRelativeURL string, oldIngressConfig *fv1.IngressConfig) (*fv1.IngressConfig, error)`.
    *   When called with a non-empty `tls` value that is not '-', set the `TLS` field of the returned IngressConfig to this value.
    *   If `oldIngressConfig` exists, overwrite its `TLS` field with the new value.
    *   If `tls` is '-', set the `TLS` field of the returned IngressConfig to an empty string.
    *   If `tls` is an empty string, preserve the existing `TLS` field value.

*   Implement the getIngressTLS function in `pkg/fission-cli/cmd/httptrigger/parse.go`:
    *   Signature: `getIngressTLS(secret string) (remove bool, tls string)`.
    *   Return `(true, '')` if `secret` is '-'.
    *   Return `(false, '')` if `secret` is an empty string.
    *   For any other non-empty `secret`, return `(false, secret)`.

*   Update the GetIngressSpec function in `pkg/router/util/util.go`:
    *   Include TLS configuration in the returned Kubernetes Ingress spec when `IngressConfig.TLS` is non-empty.
    *   Set `Spec.TLS` to a slice with one entry containing `Hosts` set to `[]string{IngressConfig.Host}` and `SecretName` set to `IngressConfig.TLS`.
    *   When `IngressConfig.TLS` is empty, ensure `Spec.TLS` is nil or empty.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.