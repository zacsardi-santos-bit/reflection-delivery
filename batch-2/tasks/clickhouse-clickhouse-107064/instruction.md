I'm hitting a bug when I do a compound column rename that swaps names between two columns of different types.

*   When a compound ALTER TABLE RENAME COLUMN operation swaps names between two columns of different types (e.g., rename column A to B while simultaneously renaming column B to A), the resulting part metadata must record the correct type for each column — each renamed column must inherit the type of the column it was renamed from, not the type of the same-named column that was renamed away.

*   This correct type assignment must apply to both compact MergeTree parts (min_bytes_for_wide_part set to a large value) and wide MergeTree parts (min_bytes_for_wide_part = 0).

*   When a compound rename swaps names and one of the source columns was never materialized (added via ALTER TABLE ADD COLUMN but never filled with data), the operation must succeed without a LOGICAL_ERROR. The unmaterialized column's data must be absent from the part and default-filled (zero value) by the reader, while the materialized column retains its values under the new name.

*   When an ALTER statement combines an unmaterialized column name swap with a simultaneous forced part rewrite (e.g., via an UPDATE in the same ALTER), both the rename and the update must be applied correctly. The unmaterialized renamed column must be omitted from the part and default-filled at read time.

*   When an ALTER TABLE statement drops a column and renames a different column into the freed name in the same operation, and the two columns have different types, the resulting column must have the type and data of the renamed-from column, not the type of the dropped column.

*   After any of the above ALTER operations, DESCRIBE TABLE must show the correct final column names and types, and SELECT queries on the renamed columns must return the expected values consistent with the data that was stored in the source columns before the rename.


*   Interface details: Type: Function
Name: getColumnsForNewDataPart
Location: src/Storages/MergeTree/MutateTask.cpp
Description: Computes the column list (names and types) for a new data part produced by a mutation. During rename mutations, when a column in the new schema is the target of a RENAME (i.e., some other column was renamed into its name), the function must look up the type from the source column in the rename map (`renamed_columns_to_from`), not from the same-named column in the old schema. The condition for entering this type-lookup branch must be whether the column's name appears as a target in the rename map (`renamed_columns_to_from.contains(it->name)`), rather than whether the old same-named column was removed (`was_removed`). This distinction is critical for name-swap renames and for DROP + RENAME-into-freed-name patterns involving columns of different types.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.