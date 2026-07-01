# Update Settings Tooltip Translations and Add Markdown Rendering Support

## Description

The settings page tooltips in the application contain outdated, fragmented tooltip text spread across many individual translation keys. These need to be consolidated into improved, clearer descriptions under new key names. Additionally, translated text strings should support basic markdown formatting (line breaks and italic text) so that tooltip content can be expressed more richly without requiring separate markup elements.

There are two issues to address:

1. **Translation key migration**: A number of existing tooltip-related translation keys in the system need to be removed or renamed, and their values updated. Three new translation keys need to be added for a contact form documentation link. This should be applied as a database migration to all existing installations, updating records in all supported languages while preserving any custom translations users may have set.

2. **Markdown rendering in translated text**: The component used to render translated strings does not currently support any formatting. Translated strings containing newlines should display with proper line breaks, and text wrapped in asterisks should render in italics. This makes it possible for tooltip descriptions to contain multi-paragraph text and emphasis without requiring the source to manually insert HTML elements.

## Expected Behavior

- The migration must run and update the database records for all locales, renaming certain tooltip keys and updating their values.
- Three new translation entries for the contact form help link must be added to the system context.
- Outdated keys that are no longer used must be removed from the system context.
- The translation rendering component must display newlines as line breaks in rendered output.
- Text surrounded by asterisks in a translated string must be rendered as italic text.

## Why This Matters

Improving tooltip text makes the settings page more understandable to users. Supporting basic markdown formatting in translation values gives content editors more flexibility to write clearer, structured help text without requiring developer intervention for every formatting change.
