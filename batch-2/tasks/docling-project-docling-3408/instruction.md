I noticed a few issues with the CLI's help output that should be cleaned up.

*   The CLI help output for the input format selection option must contain the phrase 'Input formats to' as part of its description.

*   The CLI help output for the input format selection option must contain the phrase 'all supported' to describe the default behavior when no format is specified.

*   The CLI help output for the debug layout visualization option must contain the correctly spelled phrase 'layout clusters' (not 'layour clusters').

*   The CLI help output must not contain the misspelling 'layour' anywhere.

*   The CLI help output must not expose the raw internal parameter name 'input_sources'; the positional argument for input files must use a different name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.