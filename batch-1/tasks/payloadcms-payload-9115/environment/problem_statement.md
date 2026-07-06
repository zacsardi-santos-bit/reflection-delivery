## Description

The utility responsible for converting string relationship IDs in stored MongoDB documents to native database ID objects is producing incorrect output when the input data has sparse fields — fields that are absent or missing from some documents. Instead of skipping absent fields, the traversal logic fills them with empty default values, corrupting the document structure.

## Expected Behavior

- When the conversion utility processes a document's relationship fields, it must only convert string IDs that are actually present in the data — it should never add new fields or inject empty default values for fields that are absent.
- All string hex IDs found in relationship fields should be converted to native ID objects with the same underlying value, across all nesting contexts: arrays, blocks, groups, rows, and tabs — including localized variants of each.
- Both single-value relationships and multi-value (hasMany) relationships must be handled, as well as polymorphic relationships (where the relationship stores both the target collection name and the ID value).

## Why This Matters

Developers running data migration scripts or any processing step that converts stored relationship IDs rely on this utility to produce correct, unmodified document structure. Documents with missing optional relationship fields should pass through unchanged except for the actual ID conversions. The current behavior introduces phantom empty fields that break downstream operations.
