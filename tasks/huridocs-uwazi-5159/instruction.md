Update the translation system in the application to improve tooltip descriptions and support markdown formatting. Implement a database migration to update translation keys and values, and enhance the rendering component to handle markdown for line breaks and italics.

*   Implement the migration module in `app/api/migrations/migrations/113-update_translations_of_settings_tooltips/index.js`:
    *   Ensure the module exports a `delta` property with the value `113`.
    *   Export an `up(db)` function that updates the 'translations' collection in MongoDB:
        *   Update the 'Uwazi UI' context entries across all locales.
        *   Add a key named 'Landing page description' with specified values for English, Portuguese, and Spanish.
        *   Add three new key-value pairs: 'Click', 'here', and 'to learn how to add and configure a contact form on a webpage.'
        *   Remove outdated keys from the 'Uwazi UI' context while preserving existing keys not listed for removal.

*   Enhance the `Translate` component in `app/react/I18N/components/Translate.js`:
    *   Ensure it is a named export in addition to the default connected export.
    *   Modify the `render()` method to:
        *   Parse newline characters (`\n`) and render them as self-closing `<br/>` elements.
        *   Parse asterisk-delimited text (e.g., *italic*) and render it as `<i>` elements.
        *   Produce a `<span class="translation">` element with lines joined by `<br/>` tags and italic text rendered correctly.
    *   Maintain the existing export of `mapStateToProps` from this file.

*   Update the `PublicForm` component to include a `label` field in template properties:
    *   Ensure properties like `{ type: 'text', name: 'text', label: 'Text' }` and `{ type: 'image', name: 'image', label: 'Image' }` are used.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.