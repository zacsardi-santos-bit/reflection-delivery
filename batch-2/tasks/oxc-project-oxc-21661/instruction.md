I'm using a code formatter with a configuration file that supports an overrides section — the idea being that you can apply different formatting settings to specific files or globs.

*   When a configuration file has an invalid root-level option (e.g., a line width value outside the allowed range of 1 to 320), the formatter must exit with code 1, produce no stdout output, and write to stderr: 'Failed to parse configuration.' followed by a newline, then 'Invalid printWidth: The line width should be between 1 and 320' followed by a newline.

*   When a configuration file's overrides section results in an invalid resolved option for a specific file (e.g., a line width value outside 1–320), the formatter must exit with code 2, print 'Checking formatting...' followed by a newline to stdout, and write to stderr a structured diagnostic error: indented 'x Invalid resolved configuration for <filepath>' and indented 'help: Invalid printWidth: The line width should be between 1 and 320', followed by 'Error occurred when checking code style in the above files.'

*   When a configuration file's overrides section creates a conflicting combination of options for a specific file (e.g., both partitionByNewline: true and newlinesBetween: true active simultaneously in sortImports), the formatter must exit with code 2, print 'Checking formatting...' followed by a newline to stdout, and write to stderr a structured diagnostic error: indented 'x Invalid resolved configuration for <filepath>' and indented 'help: Invalid `sortImports` configuration: `partitionByNewline: true` and `newlinesBetween: true` cannot be used together', followed by 'Error occurred when checking code style in the above files.'

*   In all error cases (both root-level and override-level), no file changes should be made — the check command must not modify any files.

*   The conflict between sortImports options is detected only after overrides are merged: a configuration with partitionByNewline: true at root level and newlinesBetween: false at root level, where overrides set newlinesBetween: true, must be detected as invalid when the resolved options for a matching file combine both partitionByNewline: true and newlinesBetween: true.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.