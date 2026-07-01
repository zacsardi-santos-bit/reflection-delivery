## Description

When running multi-statement SQL scripts that include database or schema context-switching commands, the SQL lineage parser does not correctly qualify table names that appear in subsequent statements. The parser processes each statement in isolation, so it cannot associate table references with the database or schema that was activated earlier in the script.

For example, when a script starts with a statement that sets the active database to "db1" and then runs a query against "my_table", the lineage output should show "db1.my_table" — but currently, the table appears without any namespace qualification.

## Expected Behavior

- After a context-switching statement, subsequent unqualified table references should be fully qualified with the active database and/or schema.
- When a context-switch sets only the database (not the schema), any previously active schema context should be preserved.
- Fully-qualified table references (those already specifying all three name parts) should never be overridden by the current context.
- Partially-qualified references (e.g., schema.table without a database) should be merged with the active context for any missing parts.
- Different SQL dialects have different semantics for what the context-switching command targets: in some dialects the bare form sets the database, in others it sets the schema. The parser must handle each dialect's conventions correctly.
- A "default" or keyword-based context switch that is not explicitly supported for a given dialect should be ignored without errors.

## Why This Matters

Data lineage tracking of SQL scripts relies on correctly resolving the full identity of every table referenced. Without this, datasets that depend on context-based namespace resolution appear in lineage graphs as unresolved or wrong entities, making the lineage data unreliable for governance and impact-analysis use cases.
