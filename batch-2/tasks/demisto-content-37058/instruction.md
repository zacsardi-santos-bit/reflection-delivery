Implement session token validation and malop-to-incident conversion in the Cybereason integration. Ensure the session token is checked for expiration before use and re-authenticated if necessary. Enhance the malop-to-incident conversion to include comprehensive incident details.

*   Implement the `validate_jsession` function:
    *   Accept a `client` object as a parameter.
    *   Check if the stored session token is expired by comparing `valid_until` with the current time.
    *   If expired, call the `login(client)` function to obtain a new token and creation time.
    *   Update the global `HEADERS` with `Cookie` set to `"JSESSIONID={new_token}"`.
    *   Persist the new token and its expiry time in the integration context with `set_integration_context({'jsession_id': new_token, 'valid_until': creation_time + 28000})`.

*   Implement the `malop_to_incident` function:
    *   Accept a `malop` dictionary as a parameter.
    *   Raise an exception with the message "Cybereason raw response is not valid" if the input is not a dictionary.
    *   Return a dictionary with the following structure:
        *   `name`: "Cybereason Malop {guidString}"
        *   `status`: Integer mapping of malop status
        *   `dbotmirrorid`: The `guidString` value
        *   `CustomFields`: Dictionary containing:
            *   `malopcreationtime`: String representation of creation time
            *   `malopupdatetime`: String representation of last update time
            *   `malopdetectiontype`: Detection type (empty string if absent)
            *   `malopedr`: Boolean indicating if it's an EDR malop
            *   `maloprootcauseelementname`: Root cause element name (if available)
            *   `maloprootcauseelementtype`: Root cause element type (if available)

*   Ensure the `malop_to_incident` function supports:
    *   Nested (EDR) format:
        *   Extract fields from `simpleValues` and `elementValues`.
        *   Default `malopedr` to True unless `isEdr` is explicitly False.
    *   Flat format:
        *   Extract fields directly from top-level keys.
        *   Convert numeric `creationTime` and `lastUpdateTime` to strings.
        *   Default `malopedr` to False if `edr` key is absent.

*   Map malop status to incident status:
    *   Active/unread/reopened statuses (integer 1, "UNREAD", "REOPEN", empty string, or absent) map to 0.
    *   "Remediated" maps to 1.
    *   "RESOLVED" maps to 2.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.