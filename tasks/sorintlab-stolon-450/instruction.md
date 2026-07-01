Update the PostgreSQL version parser to handle version strings with trailing newline characters. Ensure it continues to correctly parse version strings without trailing newlines and those with pre-release suffixes.

*   Modify the `ParseBinaryVersion` function in `pkg/postgresql/utils.go` to:
    *   Correctly parse version strings that include a trailing newline character.
    *   Return the correct major and minor version numbers without error.
    *   Maintain existing functionality for version strings without trailing newlines and those with pre-release suffixes.

*   Ensure the function signature remains:
    *   `ParseBinaryVersion(v string) (int, int, error)`

*   Test cases:
    *   For input 'postgres (PostgreSQL) 9.6.7\n', the function must return major version 9, minor version 6, and a nil error.
    *   Continue to handle inputs like 'postgres (PostgreSQL) 9.5.x' and 'postgres (PostgreSQL) 10beta1' as previously supported.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.