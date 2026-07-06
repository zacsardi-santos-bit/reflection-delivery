Implement individual indicator lookup capabilities in the Cyberint threat intelligence feed integration. Update the API endpoint paths for the daily feed and correct the date format. Reorganize the display header translation logic to handle both feed-level and individual indicator data.

*   Update the daily indicator feed API endpoint:
    *   Use the path pattern `/ioc/api/v1/feed/daily/{date}` where `date` is in `YYYY-MM-DD` format.
    *   Modify the `retrieve_indicators_from_api` method in the `Client` class to construct URLs using this path.

*   Add individual indicator lookup functions in `FeedCyberint.py`:
    *   Implement `get_url_command(client: Client, args: dict) -> Any`:
        *   Accept `args['value']` as a URL string.
        *   Call `client.retrieve_url_from_api(value)`.
        *   Return the retrieved response.
        *   Raise `TypeError` for invalid URLs with message: '1 validation error for Request\nquery -> value\n  invalid or missing URL scheme (type=value_error.url.scheme)'.
    *   Implement `get_ipv4_command(client: Client, args: dict) -> Any`:
        *   Accept `args['value']` as an IPv4 address string.
        *   Call `client.retrieve_ipv4_from_api(value)`.
        *   Return the retrieved response.
        *   Raise `TypeError` for invalid IPv4 addresses with message: '1 validation error for Request\nquery -> value\n  value is not a valid IPv4 address (type=value_error.ipv4address)'.
    *   Implement `get_domain_command(client: Client, args: dict) -> Any`:
        *   Accept `args['value']` as a domain string.
        *   Call `client.retrieve_domain_from_api(value)`.
        *   Return the retrieved response.
        *   Raise `TypeError` for invalid domains with message: '1 validation error for Request\nquery -> value\n  string does not match regex "^(?:(?:(?:[[a-z0-9](?:[a-z0-9\\-]*[a-z0-9])?)\\.))*(?:[a-z0-9][a-z0-9\\-]*[a-z0-9])$" (type=value_error.str.regex; pattern=^(?:(?:(?:[[a-z0-9](?:[a-z0-9\\-]*[a-z0-9])?)\\.))*(?:[a-z0-9][a-z0-9\\-]*[a-z0-9])$)'.
    *   Implement `get_file_sha256_command(client: Client, args: dict) -> Any`:
        *   Accept `args['value']` as a SHA256 hex string.
        *   Call `client.retrieve_file_sha256_from_api(value)`.
        *   Return the retrieved response.
        *   Raise `TypeError` for invalid hashes with message: '1 validation error for Request\nquery -> value\n  string does not match regex "^[a-f0-9]{64}$" (type=value_error.str.regex; pattern=^[a-f0-9]{64}$)'.

*   Update the main dispatcher function:
    *   Route to `get_url_command` for 'cyberint-get-url'.
    *   Route to `get_domain_command` for 'cyberint-get-domain'.
    *   Route to `get_ipv4_command` for 'cyberint-get-ipv4'.
    *   Route to `get_file_sha256_command` for 'cyberint-get-file-sha256'.

*   Reorganize header translation functions:
    *   Rename the existing function to `ioc_header_transformer(header: str) -> str`:
        *   Map specific feed-level fields to human-readable headers.
        *   Delegate unrecognized headers to `string_to_table_header`.
    *   Add `indicator_header_transformer(header: str) -> str`:
        *   Map individual indicator fields to human-readable headers.
        *   Delegate unrecognized headers to `string_to_table_header`.

*   Extend the `Client` class with new methods:
    *   Implement `retrieve_url_from_api(value: str) -> Any` for `/ioc/api/v1/url?value={value}`.
    *   Implement `retrieve_ipv4_from_api(value: str) -> Any` for `/ioc/api/v1/ipv4?value={value}`.
    *   Implement `retrieve_domain_from_api(value: str) -> Any` for `/ioc/api/v1/domain?value={value}`.
    *   Implement `retrieve_file_sha256_from_api(value: str) -> Any` for `/ioc/api/v1/file/sha256?value={value}`.
    *   Set `base_url` directly from the integration's configured URL parameter.

*   Ensure test data JSON files exist in `test_data/` for response mocking:
    *   `test_data/url.json`
    *   `test_data/ipv4.json`
    *   `test_data/domain.json`
    *   `test_data/file_sha256.json`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.