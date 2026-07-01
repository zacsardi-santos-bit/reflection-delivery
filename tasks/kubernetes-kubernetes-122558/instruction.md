Implement the `hookClientConfig` method in the `ClientManager` type to differentiate HTTP protocol versions for webhook connections based on the target URL's host address. Allow HTTP/2 for loopback addresses and enforce HTTP/1 for non-loopback addresses.

*   Implement the `hookClientConfig` method in `staging/src/k8s.io/apiserver/pkg/util/webhook/client.go` with the signature `hookClientConfig(cc ClientConfig) (*rest.Config, error)`.
    *   Ensure the method returns a `*rest.Config` with the `NextProtos` field configured based on the URL host.
    *   For loopback addresses ('localhost', '127.0.0.1', '::1'), set `NextProtos` to nil/empty or include 'h2' to allow HTTP/2.
    *   For non-loopback addresses (e.g., 'webhook.example.com'), set `NextProtos` to ['http/1.1'] to enforce HTTP/1.
    *   Ensure the method does not return an error for valid HTTPS URLs.

*   Ensure `hookClientConfig` correctly parses the host from the `ClientConfig.URL`, handling plain hostnames, IPv4 addresses, and bracketed IPv6 addresses.

*   Maintain compatibility with the existing `ClientManager` setup flow:
    *   Allow `NewClientManager`, `SetAuthenticationInfoResolver`, and `SetServiceResolver` to be called before `hookClientConfig`.
    *   Use the configured resolvers when building the REST config in `hookClientConfig`.

*   Ensure the `ClientConfig.URL` field is used to supply the direct HTTPS URL for the webhook endpoint.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.