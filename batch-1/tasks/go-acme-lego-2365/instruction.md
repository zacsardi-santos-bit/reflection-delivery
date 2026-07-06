Implement a new DNS provider integration for ManageEngine CloudDNS in the lego ACME client library. Ensure that users can automate DNS-01 certificate challenges by configuring the provider with client credentials via environment variables. Handle missing credentials and API errors appropriately, and create necessary fixture files for testing.

*   Implement the `NewClient` function:
    *   Accept parameters: `ctx context.Context`, `clientID string`, `clientSecret string`.
    *   Return a `*Client` with unexported fields `httpClient *http.Client` and `baseURL *url.URL`.

*   Implement the `Client` struct with methods:
    *   `GetAllZones(ctx context.Context) ([]Zone, error)`: Issue a GET request to `/dns/domain`.
    *   `GetAllZoneRecords(ctx context.Context, zoneID int) ([]ZoneRecord, error)`: Issue a GET request to `/dns/domain/{zoneID}/records/SPF_TXT`.
    *   `DeleteZoneRecord(ctx context.Context, zoneID int, domainID int) error`: Issue a DELETE request to `/dns/domain/{zoneID}/records/SPF_TXT/{domainID}`.
    *   `CreateZoneRecord(ctx context.Context, zoneID int, record ZoneRecord) error`: Issue a POST request to `/dns/domain/{zoneID}/records/SPF_TXT/`.
    *   `UpdateZoneRecord(ctx context.Context, record ZoneRecord) error`: Issue a PUT request to `/dns/domain/{ZoneID}/records/SPF_TXT/{SpfTxtDomainID}/`.

*   Define the `Zone`, `ZoneRecord`, and `Record` structs with specified fields and JSON tags.

*   Create fixture files in `providers/dns/manageengine/internal/fixtures/`:
    *   `error.json`, `error_bad_request.json`, `zone_domains_all.json`, `zone_records_all.json`, `zone_record_delete.json`, `zone_record_create.json`, `zone_record_update.json`.

*   Implement constants in `providers/dns/manageengine/manageengine.go`:
    *   `EnvClientID` with value `"MANAGEENGINE_CLIENT_ID"`.
    *   `EnvClientSecret` with value `"MANAGEENGINE_CLIENT_SECRET"`.
    *   Unexported `envNamespace` with value `"MANAGEENGINE_"`.

*   Implement `NewDNSProvider` function:
    *   Read credentials from environment variables `MANAGEENGINE_CLIENT_ID` and `MANAGEENGINE_CLIENT_SECRET`.
    *   Return an error if variables are missing, formatted as `manageengine: some credentials information are missing: <MISSING_VAR_NAMES>`.

*   Implement `NewDNSProviderConfig` function:
    *   Accept a `*Config` and return an error if `ClientID` or `ClientSecret` is empty.

*   Implement `NewDefaultConfig` function to return a non-nil `*Config`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.