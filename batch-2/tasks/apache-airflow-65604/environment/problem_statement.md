## Description

Cursor-based pagination for DAG Runs silently breaks when the sort field is specified by an aliased name — a user-facing name that maps to a different underlying database column. When the system encodes cursor tokens for pagination, it reads sort-key values directly from the result row using the alias name. Because the result row only contains the underlying column name (not the alias), the read silently returns null, producing an invalid cursor token and breaking pagination after the first page.

There is also a related bug: when a sort alias maps to the primary key column, the primary key gets appended a second time to the resolved sort column list. This produces duplicate ordering columns in the generated SQL query.

## Expected Behavior

- When a sort alias maps to a plain string column name, reading the sort value for cursor encoding should follow the alias mapping and retrieve the correct value from the result row.
- When a sort alias maps to a SQL expression (rather than a simple column name string), the system should raise a clear, explicit error rather than silently returning null.
- When a sort alias already maps to the primary key column, the primary key must not be appended again to the sort column list.

## Why This Matters

Without these fixes, any endpoint that uses cursor-based pagination with aliased sort fields will silently produce corrupted cursor tokens. Clients will receive tokens that appear valid but cause the second (and subsequent) pages to return wrong or empty results. The duplicate primary-key bug could also produce malformed SQL. These are hard to debug because the failures are silent rather than loud errors.
