Rename the file inclusion filter parameter to include a descriptive "_pattern" suffix, ensuring consistency with the exclusion filter parameter. Update the search tools to apply the filter correctly when the new parameter name is used, and ensure the search description reflects the applied filter.

*   Update the GrepToolParams interface:
    *   Rename the optional file-filter field from 'include' to 'include_pattern'.
    *   Ensure the field accepts a string glob pattern (e.g., '*.js', '*.{ts,tsx}') to restrict search results to matching files.
*   Update the RipGrepToolParams interface:
    *   Rename the optional file-filter field from 'include' to 'include_pattern'.
    *   Ensure the field accepts a string glob pattern (e.g., '*.js', '*.{ts,tsx}') to restrict search results to matching files.
*   Modify GrepTool's functionality:
    *   Ensure the build() method restricts searches to files matching the include_pattern when specified.
    *   Update getDescription() to include ' in <glob>' when include_pattern is specified.
    *   Include both ' in <glob>' and the directory path in the description when both include_pattern and dir_path are specified.
*   Modify RipGrepTool's functionality:
    *   Ensure the build() method restricts searches to files matching the include_pattern when specified.
    *   Update getDescription() to include ' in <glob>' when include_pattern is specified.
    *   Include both ' in <glob>' and the directory path in the description when both include_pattern and dir_path are specified.
*   Ensure validateToolParams() method:
    *   Accepts params objects containing include_pattern for both GrepTool and RipGrepTool.
    *   Returns null to indicate valid parameters.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.