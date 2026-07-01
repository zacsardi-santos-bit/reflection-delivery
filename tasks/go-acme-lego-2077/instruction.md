Implement support for Webnames.ru as a DNS provider in the lego ACME client to automate SSL certificate issuance and renewal using DNS-01 challenges. Ensure the provider is configurable via an API key environment variable and handles DNS TXT record management. Provide clear error messages when credentials are missing.

*   Implement the `Client` struct in `providers/dns/webnames/internal/client.go`:
    *   Include an exported `HTTPClient` field of type `*http.Client`.
    *   Include a package-visible `baseURL` field of type `string`.
*   Implement the `NewClient` function in `providers/dns/webnames/internal/client.go`:
    *   Signature: `NewClient(apiKey string) *Client`
    *   Initialize a new `Client` with the provided API key.
*   Implement the `AddTXTRecord` method for `Client`:
    *   Signature: `(c *Client) AddTXTRecord(ctx context.Context, domain string, subDomain string, value string) error`
    *   Send an HTTP POST request with `Content-Type` set to `application/x-www-form-urlencoded`.
    *   Include form fields: `domain`, `type` set to `TXT`, `record` set to `subDomain:value`, and `action` set to `add`.
    *   Return `nil` on success (when API response `result` field is `OK`), otherwise return an error.
*   Implement the `RemoveTXTRecord` method for `Client`:
    *   Signature: `(c *Client) RemoveTXTRecord(ctx context.Context, domain string, subDomain string, value string) error`
    *   Send an HTTP POST request with form fields identical to `AddTXTRecord` but with `action` set to `delete`.
    *   Return `nil` on success, otherwise return an error.
*   Create fixture files:
    *   `providers/dns/webnames/internal/fixtures/ok.json` with a JSON object containing `"result": "OK"`.
    *   `providers/dns/webnames/internal/fixtures/error.json` with a JSON object containing a non-OK `"result"` value.
*   Define constants in `providers/dns/webnames/webnames.go`:
    *   `const EnvAPIKey = "WEBNAMES_API_KEY"`
    *   `const envNamespace = "WEBNAMES_"`
*   Implement the `Config` struct in `providers/dns/webnames/webnames.go` with an `APIKey` string field.
*   Implement `NewDefaultConfig` function:
    *   Signature: `NewDefaultConfig() *Config`
    *   Return a `*Config` with default values.
*   Implement the `DNSProvider` struct in `providers/dns/webnames/webnames.go`:
    *   Include a package-visible `config` field of type `*Config`.
*   Implement `NewDNSProvider` function:
    *   Signature: `NewDNSProvider() (*DNSProvider, error)`
    *   Read the API key from the `WEBNAMES_API_KEY` environment variable.
    *   Return an error with the message: "webnames: some credentials information are missing: WEBNAMES_API_KEY" if the environment variable is absent or empty.
*   Implement `NewDNSProviderConfig` function:
    *   Signature: `NewDNSProviderConfig(config *Config) (*DNSProvider, error)`
    *   Return an error with the message: "webnames: credentials missing" if `config.APIKey` is empty.
    *   Return a non-nil `*DNSProvider` with a non-nil `config` field when successful.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.