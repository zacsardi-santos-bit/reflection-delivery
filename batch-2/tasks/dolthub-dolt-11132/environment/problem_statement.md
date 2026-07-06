## Description

When a table is created on an older version of Dolt and then accessed after upgrading to a newer version, running any schema modification on the table — such as changing a column's type or dropping an unrelated column — silently changes the recorded storage encoding in the schema record for existing text, blob, JSON, and geometry columns. The actual on-disk row data is not rewritten, so it remains in the old format. Once the schema record and the on-disk data are out of sync, reading data from those columns causes a crash with errors about malformed or unexpected data structures.

## Expected Behavior

- When modifying a column's type within the same type family (e.g. widening a text column), the column's existing persisted storage encoding should be carried forward into the new schema record — not silently replaced with the current global default encoding.
- When dropping an unrelated column from a table, the storage encodings of all surviving columns must remain unchanged.
- Tables created with older versions must continue to be readable without panics or crashes after running schema changes on a newer version.

## Why This Matters

Dolt users who upgrade from an older version often have existing databases. Any ALTER TABLE operation on those databases can silently corrupt the schema metadata, turning a routine maintenance operation into a data-loss event. This is especially subtle because the data is not rewritten — the crash only appears later when someone tries to read the affected rows.
