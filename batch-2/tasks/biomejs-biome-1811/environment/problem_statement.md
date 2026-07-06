## Description

The naming convention lint rule should support a configuration option that controls whether non-ASCII characters are allowed in identifiers. Teams writing code in non-English languages naturally use accented characters or non-Latin scripts in their variable and function names, and they should be able to opt into this behavior explicitly rather than fighting against a rule that always flags such names.

Currently, there is no recognized option for this — attempting to add such an option to the rule configuration results in an error reporting an unknown key, rather than actually changing the rule's behavior.

## Expected Behavior

- A new boolean option should be available for the naming convention rule that, when set to allow non-ASCII names, suppresses diagnostics for identifiers containing non-ASCII characters.
- By default (when the option is not set), the rule should continue to reject non-ASCII identifiers with a clear diagnostic message explaining that the option must be explicitly enabled.
- The diagnostic and hint messages should clearly reference the option name so developers know exactly what to change.

## Additional Fixes

- Identifiers with consecutive underscores (e.g., two underscores in a row between words) should not be accepted as valid constant-case names. The rule should flag such names and suggest a properly formatted alternative.
- The wording of the strict-case diagnostic messages should be updated for consistency and clarity.

## Why This Matters

This enables multilingual codebases and non-English teams to use the naming convention rule without needing to disable it entirely. The fix also closes a gap in constant-case validation where malformed names were incorrectly accepted.
