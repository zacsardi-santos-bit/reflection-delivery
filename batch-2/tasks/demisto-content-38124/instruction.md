Extend the Qualys v2 integration to support fetching vulnerabilities by specific identifiers and implement a coordinated fetch flow for assets and vulnerabilities. Ensure proper error handling and state management throughout the fetch cycles.

*   Implement the `get_vulnerabilities` function:
    *   Raise a `ValueError` with the message "Either 'since_datetime' or 'detection_qids' need to be specified" if neither parameter is provided.
    *   Make a POST request to `urljoin(API_SUFFIX, "knowledge_base/vuln/?action=list")`:
        *   Use params `{"last_modified_after": since_datetime}` if `since_datetime` is provided.
        *   Use params `{"ids": ",".join(detection_qids)}` if `detection_qids` is provided.

*   Update the `Client` class:
    *   Implement the `get_vulnerabilities` method with the signature `get_vulnerabilities(since_datetime: str | None = None, detection_qids: str | None = None) -> Any`.
    *   Use POST requests with appropriate parameters based on the provided arguments.

*   Modify the `fetch_vulnerabilities` function:
    *   Accept an optional `detection_qids` parameter (a list).
    *   Fetch vulnerabilities by date if `detection_qids` is not provided, using `last_run['since_datetime']`.
    *   Return a tuple `(vulnerabilities_list, next_run_dict)` where `next_run_dict` equals `DEFAULT_LAST_ASSETS_RUN`.

*   Implement the `fetch_assets_and_vulnerabilities_by_date` function:
    *   Execute a two-stage fetch flow:
        *   In the 'assets' stage, fetch assets and call `send_data_to_xsiam` with appropriate parameters.
        *   Update the last run state with cumulative asset counts and a snapshot identifier.
        *   In the 'vulnerabilities' stage, fetch vulnerabilities and reset the state using `DEFAULT_LAST_ASSETS_RUN`.

*   Implement the `fetch_assets_and_vulnerabilities_by_qids` function:
    *   Fetch both assets and vulnerabilities using QIDs.
    *   Call `send_assets_and_vulnerabilities_to_xsiam` with cumulative counts and state information.
    *   Update the last run state with cumulative counts and a snapshot identifier.

*   Implement the `send_assets_and_vulnerabilities_to_xsiam` function:
    *   Send assets and vulnerabilities to XSIAM using `send_data_to_xsiam` twice, first for assets and then for vulnerabilities.
    *   Use `items_count='1'` if `has_next_page` is `True`, otherwise use the cumulative counts.

*   Define the `VENDOR` string constant and the `DEFAULT_LAST_ASSETS_RUN` dict constant:
    *   `DEFAULT_LAST_ASSETS_RUN` must have the structure: `{'stage': 'assets', 'next_page': '', 'total_assets': 0, 'nextTrigger': None, 'type': 1}`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.