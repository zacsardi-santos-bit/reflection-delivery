## Description

The PostgreSQL binary version parser fails to handle version strings that include a trailing newline character. When the PostgreSQL binary is invoked to report its version, the output typically ends with a newline — which is standard behavior for command-line tools. However, the current parser cannot handle this format and returns an error instead of correctly extracting the version numbers.

## Expected Behavior

- The version parser should successfully parse version strings that end with a trailing newline, returning the correct major and minor version numbers.
- For example, a typical PostgreSQL version string for version 9.6.7 followed by a newline should be recognized as major version 9, minor version 6.
- Existing version string formats (those without trailing newlines, or those containing pre-release suffixes) should continue to work correctly.

## Why This Matters

Other parts of the system rely on this version parser to determine which PostgreSQL features and behaviors to use. When the parser fails on typical binary output, any logic that depends on knowing the PostgreSQL version becomes unreliable. This causes downstream failures in version-specific behavior handling.
