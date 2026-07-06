## Description

When a collection has a localized tab that contains a group field, updating the group's content in one language incorrectly overwrites the same group's data in other languages. This breaks locale isolation for group fields inside localized tabs.

## Steps to Reproduce

1. Create a collection with a localized tab containing a group field with a text field inside it.
2. Create a document using one locale (e.g., Spanish) and populate the group's text field.
3. Update the same document using a different locale (e.g., English) and populate the group's text field with different content.
4. Retrieve the document using the first locale (Spanish) — the original Spanish value is gone or replaced with the English value.

## Expected Behavior

- Each locale's data for a group field inside a localized tab should be stored independently.
- Retrieving the document with the English locale returns the English value for the group field.
- Retrieving the same document with the Spanish locale returns the Spanish value for the group field.
- Updating one locale's group data must not affect any other locale's group data.

## Why This Matters

Content editors working in a multi-language setup rely on locale isolation to keep translations independent. When group fields inside localized tabs don't properly isolate per-locale data, editors updating one language's content can silently destroy another language's content — leading to data loss and untranslatable documents.
