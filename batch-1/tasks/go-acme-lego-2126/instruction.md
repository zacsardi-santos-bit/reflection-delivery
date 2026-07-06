Implement a new DNS provider for the Shellrent hosting platform to enable automated ACME DNS-01 certificate challenges using lego. Ensure the provider can authenticate using a username and API token, manage DNS records, and handle TTL values appropriately.

*   Implement `NewClient(username, token string) *Client` in `providers/dns/shellrent/internal/client.go`
    *   Include an authorization header formatted as 'username.token' using the constant `authorizationHeader` with value 'Authorization'.

*   Implement `ListServices(ctx context.Context) ([]int, error)` in `Client` to:
    *   Make a GET request to `/purchase` and return service IDs as `[]int`.

*   Implement `GetServiceDetails(ctx context.Context, id int) (*ServiceDetails, error)` in `Client` to:
    *   Make a GET request to `/purchase/details/{id}` and return a `*ServiceDetails` with fields ID, Name, and DomainID.

*   Implement `GetDomainDetails(ctx context.Context, id int) (*DomainDetails, error)` in `Client` to:
    *   Make a GET request to `/domain/details/{id}` and return a `*DomainDetails` with fields ID, DomainName, and DomainNameASCII.

*   Implement `CreateRecord(ctx context.Context, domainID int, record Record) (int, error)` in `Client` to:
    *   Make a POST request to `/dns_record/store/{domainID}` and return the new record ID.

*   Implement `DeleteRecord(ctx context.Context, domainID int, recordID int) error` in `Client` to:
    *   Make a DELETE request to `/dns_record/remove/{domainID}/{recordID}`.

*   Ensure all `Client` methods return errors formatted as 'code {N}: {message}' when the API returns an error.

*   Implement `TTLRounder(ttl int) int` in `providers/dns/shellrent/internal/client.go` to:
    *   Round TTL values to the nearest supported value from {3600, 14400, 28800, 57600, 86400}.

*   Implement `NewDNSProvider() (*DNSProvider, error)` in `providers/dns/shellrent/shellrent.go` to:
    *   Read credentials from `SHELLRENT_USERNAME` and `SHELLRENT_TOKEN`.
    *   Return specific error messages if credentials are missing.

*   Implement `NewDefaultConfig() *Config` in `providers/dns/shellrent/shellrent.go` to:
    *   Return a `*Config` with default values for Username and Token.

*   Implement `NewDNSProviderConfig(config *Config) (*DNSProvider, error)` in `providers/dns/shellrent/shellrent.go` to:
    *   Validate `Config` and return errors if Username or Token is missing.

*   Ensure `DNSProvider` implements `Present(domain, token, keyAuth string) error` and `CleanUp(domain, token, keyAuth string) error`.

*   Export constants `EnvUsername = 'SHELLRENT_USERNAME'` and `EnvToken = 'SHELLRENT_TOKEN'` in `providers/dns/shellrent/shellrent.go`.

*   Create JSON fixture files in `providers/dns/shellrent/internal/fixtures/`:
    *   `purchase.json`, `purchase-details.json`, `domain-details.json`, `dns_record-store.json`, `dns_record-remove.json`, `error.json`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.