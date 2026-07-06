## Description

The user stats and group management section of the app currently uses hardcoded English text throughout several helper functions. Labels like navigation links, group action buttons, bar chart axis labels, group status messages, and date range selector options are all stored as fixed English strings. This prevents the application from being localized into other languages, since there is no way to substitute translated text.

We need to refactor these helper functions so they produce translation key strings instead of hardcoded English. This way, a translation system can resolve the correct display text at render time based on the user's locale.

## Expected Behavior

- The function that builds group stats header items should accept a translation function and use it to produce all labels via standardized translation keys (rather than returning hardcoded English strings like "all my groups", "Leave Group", "Copy Join Link", "Share Group", "Manage Group").
- The function that computes bar chart date range metadata should return translation keys for the count label (day/week/month/year) and time label (minutes/hours) fields.
- The function that determines group container status messages should return translation keys for states like "log in to join", "joining", "join failed", "not found", etc. When a group error occurs, it should pass through the error's message directly rather than wrapping it in a fixed English phrase.
- The function that builds the date range selector options should use translation keys as the label values for all options (last 7 days, last 30 days, this month, etc.).
- The classifications tab in the main content area should display with normal capitalization ("Classifications") rather than all-caps ("CLASSIFICATIONS").

## Why This Matters

These changes are a prerequisite for internationalizing the user stats area of the app. Without them, strings are hardcoded in English and cannot be replaced by a translation system. After this refactor, any language supported by the translation configuration can be displayed to users without code changes.
