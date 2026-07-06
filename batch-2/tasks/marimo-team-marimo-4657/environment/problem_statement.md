## Description

There are two related improvements needed in the editor's autocomplete system:

### 1. Variable completions inside template interpolation blocks

When editing cells that support template-style variable interpolation using curly brace syntax, there is currently no autocomplete support for notebook variable names inside those blocks. Developers have to type variable names manually and rely on memory.

We need a new completion provider that:
- Suggests notebook variable names (with type metadata) when the cursor is inside a single-brace interpolation block
- Correctly suppresses completions when the cursor is outside any open block, after a closing brace, or inside a double-brace escape sequence
- Can be toggled on or off depending on context (e.g., only active for cell types that support variable interpolation)
- Integrates as a standard editor language extension so it composes with other completions in both markdown and SQL cell types

### 2. SQL autocomplete doesn't reflect updated local tables

The SQL completion store caches schema information per-connection, but the cache key does not account for local dataset tables. As a result, after loading new datasets into the session, the SQL autocomplete still shows the old set of local tables — it only updates when a new connection object is seen, not when the datasets themselves change.

The completion store needs to incorporate current local table state into its cache key so that any update to the available datasets immediately produces fresh completion suggestions on the next request.

## Expected Behavior

- Variable name completions with type details are offered inside single-brace interpolation blocks
- Completions are suppressed for double-brace sequences, positions outside open blocks, and non-word characters
- SQL autocomplete immediately reflects changes to local dataset tables without requiring session restart
- Updated connection schemas (including changed default schema) are reflected correctly in subsequent completion requests

## Why This Matters

Without these fixes, developers get no in-editor help for variable references in template strings, and SQL queries against newly loaded tables produce incomplete or incorrect completion suggestions.
