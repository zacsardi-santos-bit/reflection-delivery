## Description

The Svelte documentation site automatically generates URL anchors from the headings in the documentation. Currently, the anchor generation logic handles only ASCII text — it strips any non-ASCII characters, which means headings written in non-Latin scripts (such as Cyrillic) produce meaningless or broken anchors. This makes it impossible to publish properly-linked documentation in non-Latin languages.

We need a dedicated slug utility module that provides two distinct processing strategies:

1. **Latinizing mode** — converts non-Latin text (including Cyrillic) into its Latin equivalent, making anchors fully ASCII-safe and RFC 3986 compliant. This is the default mode for English documentation.
2. **Unicode-preserving mode** — keeps native Unicode letters in the slug unchanged, so documentation written in non-Latin scripts results in readable, human-friendly anchors.

Both strategies should correctly handle common documentation edge cases: spaces and punctuation converted to separators, dollar signs preserved (they appear frequently in API documentation), unicode symbols translated to their word equivalents, and emoji stripped. The latinizing mode should also support language-specific symbol translations.

A configuration module is also needed so the site can be set up to use either mode, with a configurable separator character and a default language code for symbol translation.

## Expected Behavior

- ASCII text is lowercased and punctuation is replaced by the configured separator
- Dollar signs are preserved in slugs and remain attached to adjacent words
- The latinizing processor converts non-ASCII letters to their Latin equivalents
- The unicode-preserving processor keeps non-ASCII letters as-is
- Directly concatenated Latin and non-Latin words are split with the separator in unicode mode
- Unicode symbols are translated to their English word equivalents by default
- Emoji are removed from output
- Language-specific symbol translations are supported in the latinizing mode

## Why This Matters

This enables the Svelte docs to be translated to non-Latin languages while maintaining well-formed, navigable URL anchors.
