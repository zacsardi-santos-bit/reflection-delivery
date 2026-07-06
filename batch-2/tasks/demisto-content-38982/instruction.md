Implement an integration for the Symantec Endpoint Security cloud portal to automate threat indicator enrichment and incident ingestion into the XSOAR platform. Ensure the integration supports scheduled incident fetching, reputation lookups for files, URLs, domains, and IP addresses, and queries for active protection status.

*   Implement the `Client` class:
    *   Instantiate with `oauth_token` and `base_url` as positional arguments.
    *   Implement `authenticate()` to call the token endpoint, store the access token in `_session_token`, and return `True` if an access token is present.

*   Implement the `ensure_max_age` function:
    *   Accept a `datetime` and return it unchanged if within the last 30 days.
    *   Return a `datetime` equal to now minus 29 days, 23 hours, and 59 minutes if older.

*   Implement the `icdm_fetch_incidents_command` function:
    *   Return a `CommandResults` object with `outputs` as the raw incident list from the API.
    *   Include a markdown table titled 'Symantec Endpoint Security EDR Incidents' with specified columns.

*   Implement the `fetch_incidents_command` function:
    *   Return a tuple of `(next_run dict, incidents list)`.
    *   Include only incidents with `type_id == 8075`.
    *   Format each incident with specified keys and formats.
    *   `next_run` dict must include 'last_fetch' with a float Unix timestamp.

*   Implement the `file_reputation_command` function:
    *   Accept a 'file' argument and return a list of `CommandResults`.
    *   Each result's `outputs` must include `indicator`, `reputation`, and `actors`.

*   Implement the `url_reputation_command`, `domain_reputation_command`, and `ip_reputation_command` functions:
    *   Accept respective argument keys and return a list of `CommandResults`.
    *   Each result's `outputs` must include specified keys.

*   Implement the `symantec_protection_file_command`, `symantec_protection_network_command`, and `symantec_protection_cve_command` functions:
    *   Accept respective argument keys and return a list of `CommandResults`.
    *   Each result's `outputs` must equal the raw API response.

*   Implement the `ensure_argument` function:
    *   Retrieve a named argument from `args` and return as a list of strings.
    *   Raise `ValueError` if the argument is absent or empty.

*   Implement the `is_filtered` function:
    *   Return `True` if the value matches any entry in the `filters` list, including subdomains.
    *   Return `False` if the `filters` list is empty.

*   Implement the `get_network_indicator_by_type` function:
    *   Return a `Common.IP`, `Common.URL`, or `Common.Domain` instance based on type.
    *   Raise `DemistoException` for unsupported or empty type values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.