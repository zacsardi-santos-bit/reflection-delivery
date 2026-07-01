Implement a slug utility module for the Svelte documentation site to generate URL anchors from headings. Create two processing strategies: one that transliterates non-Latin characters to Latin equivalents and another that preserves native Unicode letters. Ensure the module handles spaces, punctuation, dollar signs, Unicode symbols, and emoji correctly. Provide a configuration file for separator and language settings.

Requirements:

*   In `site/config.js`:
    *   Export `SLUG_SEPARATOR` with the value `'_'`.
    *   Export `SLUG_LANG` with the value `'en'`.

*   In `site/src/utils/slug.js`:
    *   Implement `limaxProcessor`:
        *   Accepts a string and an optional language code (defaulting to `SLUG_LANG`).
        *   Returns a lowercase URL-safe slug with words joined by `SLUG_SEPARATOR`.
        *   Replace spaces and punctuation (periods, equals signs, colons, slashes, commas) with `SLUG_SEPARATOR`.
        *   Collapse multiple consecutive separators into one.
        *   Preserve dollar signs and keep them attached to the following word.
        *   Transliterate non-ASCII Unicode letters to Latin equivalents.
        *   Translate Unicode symbols to their English word equivalents by default, or to language-specific words if a language code is provided.
        *   Remove emoji from the output.

    *   Implement `unicodeSafeProcessor`:
        *   Accepts a string.
        *   Returns a URL-safe slug preserving non-ASCII Unicode letters verbatim.
        *   Lowercase and sanitize ASCII portions, replacing spaces and punctuation with `SLUG_SEPARATOR`.
        *   Split directly concatenated ASCII and Unicode sequences with `SLUG_SEPARATOR`.
        *   Keep a dollar sign attached to following ASCII characters; separate it from Unicode letters with `SLUG_SEPARATOR`.
        *   Translate Unicode symbols to word equivalents and remove emoji, consistent with `limaxProcessor`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.