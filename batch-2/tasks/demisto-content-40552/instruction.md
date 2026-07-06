Refactor the GreyNoise integration to improve initialization and command handling. Implement a configuration object for client settings and update command outputs and error messages for consistency and clarity.

*   Create an `APIConfig` class in `Packs/GreyNoise/Integrations/GreyNoise/GreyNoise.py`:
    *   Accept keyword arguments: `api_key`, `api_server`, `timeout`, `proxy`, `use_cache`, `integration_name`.
*   Modify the `Client` class in `Packs/GreyNoise/Integrations/GreyNoise/GreyNoise.py`:
    *   Change constructor to accept a single `APIConfig` object.
*   Update the `main()` function:
    *   Instantiate `APIConfig` with `api_key` as a keyword argument.
    *   Pass the `APIConfig` object to the `Client`.
*   Adjust the `riot_command` function:
    *   Promote 'ip' and 'riot' fields to the top level of the output.
    *   Set 'riot' to the 'found' key value from `business_service_intelligence`, defaulting to `False`.
*   Revise the `query_command` function:
    *   Use 'GreyNoise.IP(val.address && val.address == obj.address)' for IP results.
    *   Use 'GreyNoise.Query(val.query && val.query == obj.query)' for query metadata.
*   Update error messages for specific commands:
    *   For `ip_quick_check_command`, format message on HTTP 405 as: 'Failed to execute {command_name} command.\n Error: {message}'.
    *   For `similarity_command` and `timeline_command`, format message on HTTP 404 similarly.
*   Ensure all other error conditions in commands produce messages like: 'Invalid response from GreyNoise. Response: {details}'.
*   Implement an `APIConfig` class in `Packs/GreyNoise/Integrations/GreyNoise_Community/GreyNoise_Community.py`:
    *   Accept keyword arguments: `api_key`, `api_server`, `cache_ttl`, `proxy`, `use_cache`, `integration_name`.
*   Modify the `Client` class in `Packs/GreyNoise/Integrations/GreyNoise_Community/GreyNoise_Community.py`:
    *   Change constructor to accept a single `APIConfig` object.
*   Add `get_ip_tag_names` function in `Packs/GreyNoise/Integrations/GreyNoise_Community/GreyNoise_Community.py`:
    *   Extract and return tag names from IP lookup data.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.