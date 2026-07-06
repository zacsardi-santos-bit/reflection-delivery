I'm working on two related improvements to the translation system in this application.

First, I need a database migration that updates the tooltip translations on the settings page. Several existing tooltip keys need to be renamed and their values updated to clearer descriptions. Some old keys need to be removed entirely, and three new keys need to be added for a contact form documentation link. The migration should apply these changes to all locales in the database, preserving any translations that users may have customized in non-English languages.

Second, I need the component that renders translated text to support basic markdown formatting. Right now, if a translated string contains newlines, they're lost in the rendered output — they should become line breaks. Similarly, if a translated string contains text surrounded by asterisks, that text should render as italic. This would allow tooltip text (and other translated strings) to contain multi-line, formatted content without needing separate markup.

Both changes are connected: the updated tooltip values use multiline text and italic formatting (asterisk-wrapped words), so the rendering component needs to support these formats to properly display the new tooltip content.
