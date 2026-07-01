## Description

We need two foundational utility modules to support a user feedback system for the weave trace server.

The first is an emoji normalization utility. When users leave emoji reactions on traced objects, they can choose different skin-tone variants of the same emoji. Without normalization, a thumbs-up in light skin tone and a thumbs-up in dark skin tone are treated as completely different reactions. We need a way to strip skin-tone modifiers from emoji characters and their shortcode representations so that all variants of the same reaction can be grouped together. This must handle plain emoji, ZWJ sequences (where an emoji is composed of multiple codepoints joined by a zero-width joiner), and sequences where multiple components each carry their own tone.

The second is a lightweight SQL generation layer that produces correct queries for both ClickHouse (used in production) and SQLite (used for testing). Currently there is no shared abstraction for building queries against both databases, making it hard to write portable business logic. We need utilities to define tables and columns, generate table creation and deletion SQL, and build SELECT queries with support for parameterized field selection — including extraction of sub-fields from JSON columns. The parameter binding format differs between the two databases and must be handled automatically.

## Expected Behavior

- Emoji shortcodes with skin-tone suffixes are normalized to their base form
- Emoji characters with skin-tone modifier codepoints are stripped to the base emoji
- Multi-component emoji sequences (ZWJ) have all tone components removed
- A parameter builder produces the correct placeholder format per database type
- Table DDL (create/drop) is generated from column definitions
- SELECT queries support JSON sub-field extraction in both database dialects
- Combining SQL condition strings follows correct AND/OR bracketing rules

## Why This Matters

These two modules are the building blocks for a feedback system that allows reactions and notes to be stored, normalized, and queried against traced objects. Without emoji normalization, reaction counts would be fragmented by skin tone. Without the SQL layer, the same query logic would need to be written twice — once for production and once for tests.
