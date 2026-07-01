## Description

The Lucene-based auto-complete search currently relies on a generic analyzer that cannot handle text in East Asian scripts (Chinese, Japanese, Korean), email addresses with special characters, or mixed-script content. As a result, indexing or querying text in CJK languages produces no meaningful auto-complete suggestions because the generic tokenizer treats multi-character CJK words as undivided blobs rather than breaking them into individual character tokens as required by these writing systems.

Additionally, when doing phrase auto-complete with the standard analyzer, short common words like "of" are incorrectly included in the completed-token list rather than being filtered out. This leads to incorrect phrase matching behavior where stop words and very short words pollute the token list and reduce match accuracy.

## Expected Behavior

- A new analyzer should exist that properly tokenizes CJK text character-by-character, handles email address patterns (including the "@" symbol and domain components), and supports mixed CJK and Latin scripts.
- The new analyzer should apply case-insensitive matching for Latin text, so queries with arbitrary capitalization match indexed text correctly.
- Tokens that are too short (below a configurable minimum alphanumeric length) should be excluded from the token stream. For example, with a minimum length of 3, common two-letter words are filtered out.
- A Lucene index configured to use this new analyzer should correctly return auto-complete suggestions for CJK phrases, email-like strings, and mixed-language content.
- When parsing a phrase query for auto-complete, short words must be excluded from the list of completed tokens rather than included.

## Why This Matters

Developers building search features over content that includes East Asian languages or email addresses currently have no way to get meaningful auto-complete behavior from Lucene indexes in this system. This change enables a new index configuration option that activates the CJK- and email-aware analyzer, making auto-complete suggestions work correctly for these text types.
