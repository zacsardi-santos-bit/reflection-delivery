I'm working on a data query system that uses incremental view maintenance, and I need to add support for fetching rows by a list of specific value combinations — essentially the equivalent of a SQL IN clause across one or more columns.

*   A new MultiConstraint type must be exported from packages/zql/src/ivm/operator.ts, representing a readonly array of partial row objects where each element defines a set of column-value pairs that a row must fully match to be included in results.

*   The FetchRequest type in packages/zql/src/ivm/operator.ts must gain an optional multiConstraints field of type MultiConstraint[]. When this field is present and non-empty, a fetch must return only rows that match at least one entry in every element of the array (each element is an IN list; multiple elements are ANDed together).

*   An empty outer multiConstraints array ([]) must be treated as a no-op: all rows are returned as if the field were absent.

*   An empty inner MultiConstraint element ([]) within the multiConstraints array must be ignored and not treated as a filtering constraint.

*   When multiple MultiConstraint elements are present in multiConstraints, they must all be ANDed together: a row must match at least one entry in each element to be included.

*   The multiConstraints field must compose correctly with the existing constraint field (both applied as ANDed conditions), with reverse ordering, and with a start-based pagination cursor.

*   NULL column values in a row must never match a NULL entry in an IN list (SQL NULL ≠ NULL semantics): rows with a null value in a constrained column and entries with null values in an IN list both silently fail to match.

*   Compound-key IN lists (entries with multiple fields) must require all fields in an entry to be present and equal in the row for a match; a partial field match is not sufficient.

*   A row missing a column that appears in the constraint entries of a MultiConstraint must not match any entry.

*   The overlaysForMultiConstraintForTest function must be exported from packages/zql/src/ivm/memory-source.ts and filter an overlay's add and remove sides independently through a single MultiConstraint IN list, returning undefined for any side whose row does not match any entry.

*   The Overlay type must be exported from packages/zql/src/ivm/memory-source.ts.

*   The generateWithOverlay function must accept an optional multiConstraints parameter as its 8th argument. The overlay's add and remove sides must each be filtered through every element of multiConstraints (ANDed) before injection. An absent or empty outer array is a no-op; an empty inner element is ignored.

*   The generateWithOverlayUnordered function must accept an optional multiConstraints parameter as its 7th argument with the same filtering semantics as generateWithOverlay.

*   The buildSelectQuery function in packages/zqlite/src/query-builder.ts must accept multiConstraints as its 8th argument and include the corresponding WHERE clause: for a single-column list it emits "col" IN (?,?,?); for a compound-key list it emits ("col1","col2") IN (VALUES (?,?),(?,?)).

*   The multiConstraintToSQL function must be exported from packages/zqlite/src/query-builder.ts. It must throw an error with the message 'multiConstraint must be non-empty' for an empty array, throw an error with the message 'multiConstraint entries must have at least one key' for entries with no keys, and throw an error matching the pattern /share the same keys/ for entries whose key sets differ from one another.


*   Interface details: Type: TypeAlias
Name: MultiConstraint
Location: packages/zql/src/ivm/operator.ts
Description: A type representing a single IN-list constraint: a readonly array of partial row objects where each element defines one set of column-value pairs that a row must fully match. Used as MultiConstraint[] (an array of independent IN lists to be ANDed) in FetchRequest.multiConstraints.

Type: TypeAlias
Name: Overlay
Location: packages/zql/src/ivm/memory-source.ts
Description: Type representing an in-progress pending change, with shape { epoch: number, change: SourceChange }. Must be exported so tests can reference it by name.

Type: Interface/Type (field addition)
Name: FetchRequest
Location: packages/zql/src/ivm/operator.ts
Description: The existing FetchRequest type must gain a new optional field: multiConstraints?: MultiConstraint[]. When present and non-empty, only rows that match at least one entry in each individual MultiConstraint are returned. An empty outer array ([]) is a no-op (all rows returned). An empty inner MultiConstraint ([]) is ignored (treated as no constraint). Multiple entries in the outer array are ANDed together. The field must also interact correctly with the existing `constraint`, `reverse`, and `start` fields.

Type: Function
Name: overlaysForMultiConstraintForTest
Location: packages/zql/src/ivm/memory-source.ts
Signature: overlaysForMultiConstraintForTest(overlay: {add: Row | undefined, remove: Row | undefined}, multiConstraint: MultiConstraint) -> {add: Row | undefined, remove: Row | undefined}
Description: Test-exported helper that filters an overlay's add and remove sides through a single MultiConstraint IN list. Each side is kept only if its row matches at least one entry in the list; otherwise that side is set to undefined. Matching requires ALL fields in an entry to be present and equal in the row. A row missing a constrained column does not match. An empty multiConstraint array is treated as a no-op (returns overlay unchanged). Add and remove sides are filtered independently.

Type: Function
Name: generateWithOverlay
Location: packages/zql/src/ivm/memory-source.ts
Signature: generateWithOverlay(startAtOverlay: ... | undefined, iteratorRows: Iterable<Row>, constraint: ... | undefined, overlay: Overlay, epoch: number, compare: (a: Row, b: Row) => number, filter: ... | undefined, multiConstraints?: readonly MultiConstraint[]) -> Iterable<Node>
Description: Existing ordered overlay generator updated to accept an optional multiConstraints parameter as its 8th argument. When provided, the overlay's add and remove sides are each filtered through every MultiConstraint in the list (all must match) before injection. An empty inner MultiConstraint is ignored. An absent or empty multiConstraints is a no-op.

Type: Function
Name: generateWithOverlayUnordered
Location: packages/zql/src/ivm/memory-source.ts
Signature: generateWithOverlayUnordered(iteratorRows: Iterable<Row>, constraint: ... | undefined, overlay: Overlay, epoch: number, pk: readonly string[], filter: ... | undefined, multiConstraints?: readonly MultiConstraint[]) -> Iterable<Node>
Description: Existing unordered overlay generator updated to accept an optional multiConstraints parameter as its 7th argument. Filters the overlay's add/remove sides through the multiConstraints list before injection, with the same semantics as the ordered variant.

Type: Function
Name: buildSelectQuery
Location: packages/zqlite/src/query-builder.ts
Signature: buildSelectQuery(table: string, columns: Record<string, SchemaValue>, constraint: Constraint | undefined, ..., sort: ..., reverse: boolean | undefined, start: Start | undefined, multiConstraints: readonly MultiConstraint[] | undefined) -> Statement
Description: Existing SQL SELECT builder updated to accept an optional multiConstraints parameter as its 8th argument. For a single-column MultiConstraint, emits a WHERE clause of the form "col" IN (?,?,?). For a compound-key MultiConstraint, emits ("col1","col2") IN (VALUES (?,?),(?,?)). Multiple entries in multiConstraints are ANDed together in the WHERE clause, combined with any constraint and start cursor that are also present.

Type: Function
Name: multiConstraintToSQL
Location: packages/zqlite/src/query-builder.ts
Signature: multiConstraintToSQL(multiConstraint: MultiConstraint, columns: Record<string, SchemaValue>) -> {text: string, values: Value[]}
Description: Converts a single MultiConstraint IN list into a SQL fragment and bound parameter values. For a single-column list, produces "col" IN (?,?,?). For compound keys, produces ("col1","col2") IN (VALUES (?,?),(?,?)). Throws an error with message 'multiConstraint must be non-empty' if the array is empty. Throws an error with message 'multiConstraint entries must have at least one key' if any entry has no keys. Throws an error matching the pattern /share the same keys/ if entries have heterogeneous key sets (different keys across entries).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.