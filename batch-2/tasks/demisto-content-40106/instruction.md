Implement a new integration for the European Union Vulnerability Database (EUVD) in the XSOAR platform. Ensure the integration allows querying the database for advisories and vulnerabilities using various identifiers and criteria. Include a connectivity test to verify API reachability.

*   Create a new integration module at `Packs/EUVD/Integrations/EuropeanUnionVulnerabilityDatabase/EuropeanUnionVulnerabilityDatabase.py`.
    *   Implement the `Client` class extending `BaseClient` with methods:
        *   `get_advisory_by_id_request(advisory_id) -> dict`
        *   `get_by_enisa_id_request(enisa_id) -> dict`
        *   `get_latest_critical_vulnerabilities_request() -> list`
        *   `get_latest_exploited_vulnerabilities_request() -> list`
        *   `get_vulnerability_by_id_request(vulnerability_id) -> dict`
        *   `query_vulnerabilities_request(from_score, to_score, from_epss, to_epss, from_date, to_date, product, vendor, assigner, exploited, page, text, size) -> dict`
    *   Ensure the class constructor accepts `server_url`, `verify`, `proxy`, and `headers`.

*   Implement command functions:
    *   `get_advisory_by_id_command(client: Client, args: dict) -> CommandResults`
        *   Raise `ValueError` with "The 'advisory_id' argument is required." if `args['advisory_id']` is missing.
        *   Return `CommandResults` with `outputs_prefix="EUVD.Advisory"`.
    *   `get_vulnerability_by_enisa_id_command(client: Client, args: dict) -> CommandResults`
        *   Raise `ValueError` with "The 'enisa_id' argument is required." if `args['enisa_id']` is missing.
        *   Return `CommandResults` with `outputs_prefix="EUVD.Vulnerability"`.
    *   `get_vulnerability_by_id_command(client: Client, args: dict) -> CommandResults`
        *   Raise `ValueError` with "The 'vulnerability_id' argument is required." if `args['vulnerability_id']` is missing.
        *   Return `CommandResults` with `outputs_prefix="EUVD.Vulnerability"`.
    *   `get_latest_critical_vulnerabilities_command(client: Client, args: dict) -> CommandResults`
        *   Return `CommandResults` with `outputs_prefix="EUVD.Vulnerability"`.
    *   `get_latest_exploited_vulnerabilities_command(client: Client, args: dict) -> CommandResults`
        *   Return `CommandResults` with `outputs_prefix="EUVD.Vulnerability"`.
    *   `get_latest_vulnerabilities_command(client: Client, args: dict) -> CommandResults`
        *   Use `client._http_request` to fetch data.
        *   Return `CommandResults` with `outputs_prefix="EUVD.Vulnerability"`.
    *   `query_vulnerabilities_command(client: Client, args: dict) -> CommandResults`
        *   Pass all 13 filter criteria from `args` to `client.query_vulnerabilities_request`.
        *   Return `CommandResults` with `outputs_prefix="EUVD.Vulnerability"`.

*   Implement `test_module(client: Client) -> str`
    *   Return "ok" on successful HTTP 200 response.
    *   Propagate `DemistoException` on HTTP errors.

*   Implement `main() -> None`
    *   Read and configure the server URL from `demisto.params()`.
    *   Route commands using `demisto.command()`.
    *   Handle "test-module" by calling `test_module()`.

*   Ensure test data JSON files exist at specified paths within `test_data/`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.