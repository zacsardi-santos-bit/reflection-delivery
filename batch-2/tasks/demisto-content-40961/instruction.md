Implement automation scripts for case management, including searching for cases by time range and retrieving detailed case data. Extend the core integration to support a new command for retrieving cases with normalized field names and enforced query limits.

Requirements:

*   Implement the `extract_ids` function in `Packs/CommonScripts/Scripts/GetCaseExtraData/GetCaseExtraData.py`:
    *   Accept a dict, list of dicts, None, or other types.
    *   Return a list of values for a specified field name.
    *   Handle non-dict/non-list inputs by returning an empty list.

*   Implement the `get_case_extra_data` function in `Packs/CommonScripts/Scripts/GetCaseExtraData/GetCaseExtraData.py`:
    *   Call `execute_command` with 'core-get-case-extra-data' and args.
    *   Return a dict merging case data with `issue_ids`, `network_artifacts`, and `file_artifacts`.
    *   Default `issue_ids` to an empty list if missing.

*   Implement the `main` function in `Packs/CommonScripts/Scripts/GetCaseExtraData/GetCaseExtraData.py`:
    *   Call `get_case_extra_data` and handle results or exceptions with `return_results` and `return_error`.

*   Implement the `prepare_start_end_time` function in `Packs/CommonScripts/Scripts/SearchCases/SearchCases.py`:
    *   Modify args to set `gte_creation_time` and `lte_creation_time` based on parsed `start_time` and `end_time`.
    *   Raise `DemistoException` if `end_time` is provided without `start_time`.

*   Implement the `main` function in `Packs/CommonScripts/Scripts/SearchCases/SearchCases.py`:
    *   Call `prepare_start_end_time`, execute the core cases command, and handle results or errors.

*   Implement the `replace_substring` function in `Packs/Core/Integrations/CortexPlatformCore/CortexPlatformCore.py`:
    *   Replace substrings in strings or dict keys.
    *   Return modified strings or dicts, leaving other types unchanged.

*   Implement the `preprocess_get_cases_outputs` function in `Packs/Core/Integrations/CortexPlatformCore/CortexPlatformCore.py`:
    *   Normalize field names by replacing "incident" with "case" and "alert" with "issue".

*   Implement the `preprocess_get_case_extra_data_outputs` function in `Packs/Core/Integrations/CortexPlatformCore/CortexPlatformCore.py`:
    *   Rename keys in dicts or lists, transforming nested keys as specified.

*   Implement the `preprocess_get_cases_args` function in `Packs/Core/Integrations/CortexPlatformCore/CortexPlatformCore.py`:
    *   Ensure `args['limit']` does not exceed `MAX_GET_INCIDENTS_LIMIT` (100).

*   Implement the `get_cases_command` function in `Packs/Core/Integrations/CortexPlatformCore/CortexPlatformCore.py`:
    *   Convert `case_id_list` to a list of strings.
    *   Enforce a maximum limit of 100.
    *   Raise `ValueError` for missing filters or conflicting time filters.
    *   Call `client.get_incidents` with specified parameters and return `CommandResults`.

*   Define `MAX_GET_INCIDENTS_LIMIT` as 100 in `Packs/Core/Integrations/CortexPlatformCore/CortexPlatformCore.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.