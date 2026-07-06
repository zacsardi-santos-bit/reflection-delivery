## Description

The file search tool already protects users from a common mistake: when a search pattern contains a path separator, the tool warns them and exits with an error rather than silently returning zero results. This is because patterns are matched against file names, not full paths, so a slash in a pattern can never produce any matches without enabling full-path mode.

However, this protection only applies to the primary search pattern. Additional patterns supplied through the "and" flag receive no such validation. If a user accidentally pastes a full path as an "and" pattern, the search quietly returns nothing — no error, no hint about what went wrong.

## Expected Behavior

- When any "and" pattern contains a path separator, the tool should fail immediately and display the same diagnostic it shows for the primary pattern: that the search pattern contains a path-separation character and will not lead to any results.
- When full-path matching is explicitly enabled, path separators in "and" patterns should be treated as intentional (exactly as they are for the primary pattern), and the search should proceed normally.

## Why This Matters

This is an easy footgun: the same mistake that triggers a clear error for the main pattern produces silent empty output when made in an "and" pattern. Making the validation consistent across all search patterns gives users uniform, predictable feedback and reduces confusion.
