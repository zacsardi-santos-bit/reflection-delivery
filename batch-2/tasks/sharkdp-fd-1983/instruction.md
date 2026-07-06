I'd like to add a new search mode to the tool that does a true exact filename match.

*   Must support a new CLI flag named '--exact' that performs literal, whole-filename matching. When '--exact' is used, the pattern must match the complete filename (not a substring), and all characters in the pattern (including dots, parentheses, and other special characters) are treated as literal characters, not as regular expression metacharacters.

*   When '--exact' is used without '--case-sensitive', matching must be case-insensitive by default. For example, the pattern 'download (1).tar.gz' must match a file named 'Download (1).tar.gz'.

*   When '--exact' is combined with '--case-sensitive', matching must be case-sensitive. A pattern that differs in case from the actual filename must produce no results.

*   When '--exact' is used, the pattern must match the entire filename, not a substring. For example, '--exact a.foo' must match 'a.foo' but must NOT match 'aa.foo', 'a.food', or 'ca.food'. Similarly, '--exact download (1)' must NOT match 'Download (1).tar.gz' because the pattern does not cover the full filename.

*   When '--fixed-strings' is combined with '--case-sensitive', a pattern that exactly matches the case of a substring in the filename must return that file as a result. For example, '--fixed-strings --case-sensitive Download (1)' must match 'Download (1).tar.gz'.


*   Interface details: Type: CLI Flag
Name: --exact
Location: src/cli.rs (Opts struct)
Signature: --exact (boolean flag, no value argument)
Description: Performs literal, whole-filename matching. The pattern is treated as a fixed string (all characters are literal, not regex metacharacters), and the pattern must match the entire filename rather than a substring. Case-insensitive by default; can be combined with --case-sensitive for strict matching. Conflicts with --glob and --fixed-strings.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.