Fix the bugs in the remap configuration parsing for named access control filters. Ensure that redefining a named filter updates the definition without crashing and that filters without IP restrictions match all clients. Implement error handling for multiple action directives in a single filter definition.

*   Update `remap_parse_config_bti` function:
    *   Declare `remap_parse_config_bti` as a public function in `include/proxy/http/remap/RemapConfig.h`.
    *   Ensure it returns `false` if a `.definefilter` directive contains more than one `@action=` parameter.
    *   Ensure it returns `true` when a named filter is defined twice with separate `.definefilter` directives, updating the `BUILD_TABLE_INFO` rules_list to reflect the last definition's action.
*   Handle named filters without IP restrictions:
    *   When such a filter is activated for a map rule, ensure the resulting filter applied to the `url_mapping` has `src_ip_cnt == 1`, `src_ip_valid` set to a non-zero value, and `src_ip_array[0].match_all_addresses == true`.
*   Update `UrlRewrite` class methods:
    *   Ensure `BuildTable` returns `TS_SUCCESS` when parsing a valid remap config using `.definefilter`, `.activatefilter`, and `.deactivatefilter` directives.
    *   Ensure `rule_count` returns the exact number of forward map rules loaded from the config.
    *   Ensure `forwardMappingLookup` returns `true` and populates the `UrlMappingContainer` when a matching rule exists for the given URL, port, and host.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.