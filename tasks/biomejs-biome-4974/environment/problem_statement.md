## Description

A recent major version of Biome replaced the separate include and ignore configuration fields with a new unified field that handles both inclusion and exclusion of files using negation prefixes. The migration tool should automatically detect when a configuration file still uses the old separate-field style and offer a code fix to convert it to the new format.

Currently, when users run migration on their configuration files, the tool does not handle the conversion of these fields. Users are left to manually migrate their file-filter settings, understand the new glob syntax transformations, and ensure that both include and ignore patterns are merged correctly.

## Expected Behavior

- When a configuration contains the old-style separate include or ignore fields (in any relevant section: global files settings, formatter, linter, assists, or overrides entries), a fixable diagnostic should be reported.
- The suggested code fix should replace both fields with a single unified field containing all patterns correctly transformed to the new glob format.
- If only an ignore list is present (no include), the fix should prepend a catch-all wildcard so that the exclusions work correctly.
- If certain glob patterns use syntax that is incompatible with the new format (for example, single-character wildcards or character class brackets), each such pattern should be reported individually with an explanation of why it cannot be converted, and those patterns should be omitted from the fix.
- The migration should work across all relevant configuration sections including per-tool settings and per-file override entries.
- Configuration files that use comments and trailing commas (JSONC format) should be fully supported — the tool should parse them correctly, and any comments between the merged fields should be cleaned up as part of the fix.

## Why This Matters

Users upgrading to the latest major version of Biome need a smooth, automated path to update their configuration files. Without this migration rule, they must manually figure out the glob conversion rules and update every occurrence of these fields across potentially complex configuration files. An automated migration with clear error reporting for incompatible patterns significantly reduces the upgrade burden.
