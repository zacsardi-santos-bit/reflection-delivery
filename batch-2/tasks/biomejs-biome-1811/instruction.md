Implement a configuration option for the naming convention lint rule to allow non-ASCII characters in identifiers. Ensure that identifiers with consecutive underscores are flagged as invalid constant-case names, and update diagnostic messages for clarity and consistency.

*   Add a new configuration option `requireAscii` to the naming convention lint rule.
    *   Recognize `requireAscii` as a valid key in the rule's options JSON.
    *   Include `requireAscii` in the list of accepted keys when reporting unknown option errors.

*   Configure the behavior of the `requireAscii` option:
    *   When `requireAscii` is set to `false`, allow identifiers with non-ASCII characters without producing diagnostics.
    *   When `requireAscii` is not set (defaults to `true`), produce a diagnostic for non-ASCII identifiers in top-level const declarations.
        *   Error message: 'This top-level const name should be in ASCII because requireAscii` is set to `true`.'
        *   Info note: 'If you want to use non-ASCII names, then set the requireAscii option to `false`.\nSee the rule options for more details.'

*   Validate constant-case names:
    *   Flag identifiers with consecutive underscores as invalid.
    *   Error message for such identifiers: 'This top-level const name should be in camelCase or PascalCase or CONSTANT_CASE.'
    *   Suggest renaming to camelCase (e.g., MY__CONSTANT should suggest myConstant).

*   Update diagnostic messages for strict-case validation:
    *   Error message for two consecutive uppercase characters: 'Two consecutive uppercase characters are not allowed in camelCase and PascalCase because strictCase is set to `true`.'
    *   Info note: 'If you want to use consecutive uppercase characters in camelCase and PascalCase, then set the strictCase option to `false`.\nSee the rule options for more details.'

*   Ensure the configuration interface for `requireAscii` is used correctly:
    *   Type: boolean
    *   Default: true
    *   Effect when `false`: Non-ASCII identifiers are accepted without diagnostic.
    *   Effect when `true` (or absent): Non-ASCII identifiers trigger a diagnostic.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.