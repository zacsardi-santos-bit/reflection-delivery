## Description

The slash command autocomplete in the CLI currently only surfaces suggestions when the user types the exact beginning of a command name. This means mixed-case input, skipped characters, or near-matches return no suggestions, making the feature brittle and hard to use.

## Expected Behavior

- Typing a partial command name in any mix of upper and lower case should still return matching suggestions (case-insensitive matching).
- Typing a query with some characters missing from a command name (but still in order) should still surface that command as a suggestion (fuzzy/subsequence-based matching).
- When the fuzzy search engine encounters an internal error, the completion system should fall back to simpler prefix-based matching rather than showing nothing, so users still get useful suggestions.
- When that fallback is triggered, the error should be logged to the console so developers can investigate.
- The underlying search engine instance should be reused across keystrokes for the same command list, rather than recreated on every input change.
- When a query matches both a command's primary name and one of its alternative names, the command should only appear once in the suggestion list.

## Why This Matters

Without fuzzy and case-insensitive matching, users who type commands with incorrect capitalization or minor typos get no autocompletion help at all. The fallback and error-logging requirements ensure the system degrades gracefully rather than silently failing, and the caching improvement reduces unnecessary work on every keystroke.
