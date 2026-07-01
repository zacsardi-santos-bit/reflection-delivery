## Description

When duplicating a document that has localized blocks containing nested arrays, or localized groups inside tab fields that themselves contain nested array fields, the duplicated document does not correctly retain locale-specific data.

The root of the problem appears to be that when a document is duplicated in a relational database setup, new record IDs need to be generated for each block and array row. This ID regeneration is not being done recursively for all nested structures. Additionally, when arrays are nested inside a localized group that lives within a tab field, the locale data for those arrays is dropped or corrupted during duplication.

## Expected Behavior

- Duplicating a document with localized blocks that contain nested arrays should produce a new document where all locale-specific data in those nested arrays is preserved correctly.
- Duplicating a document with localized groups inside tabs — where those groups contain nested array fields — should produce a new document where every locale's data (including array items) is preserved as-is.
- In a relational database, the duplication process must generate new unique IDs for all block and array rows at every level of nesting, not just the top level.

## Why This Matters

Content editors working with multi-locale content rely on the duplicate feature to quickly create similar records. If duplication silently drops or corrupts locale-specific nested data, editors may unknowingly publish incomplete or incorrect content. This is especially impactful for documents with complex nested field structures like blocks containing arrays and localized groups inside tabs.
