I'm using the oxfmt formatter CLI and I'm running into confusing behavior when files I explicitly pass to the tool are excluded by my ignore configuration.

*   When all user-specified files are excluded by ignore rules and the tool exits with an error (exit code 2), the message written to stderr must be exactly: "Expected at least one target file. All matched files may have been excluded by ignore rules."

*   When the formatter runs in check mode with explicit file paths that are all excluded by ignore rules (resulting in exit code 2), the standard output must include "Checking formatting..." before the error is reported.

*   When the --no-error-on-unmatched-pattern flag is used and all specified files are excluded by ignore rules, the process must exit with code 0, standard output must include "Checking formatting..." and a completion line formatted as "Finished in <time>ms on 0 files using <N> threads.", and standard error must include "No files found matching the given patterns."

*   The above behaviors must apply consistently across different ignore rule sources: explicit --ignore-path flag overrides, configuration-based ignores, prettierignore-based ignores, and custom ignore files.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.