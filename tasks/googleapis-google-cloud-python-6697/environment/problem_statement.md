## Description

Firestore query cursors currently only accept a dictionary or a document snapshot to define a pagination position. There is no way to specify a cursor as a plain ordered list or tuple of values, which is a more direct and convenient format when the field ordering is already known. Additionally, the current implementation allows specifying a cursor before defining the query's sort order, which can silently produce incorrect results since the cursor validation only happens at serialization time without checking that the ordering was already established.

## Expected Behavior

- Query cursors should accept a plain list or tuple of ordered values (in addition to dicts and document snapshots) to define a position in a result set.
- When a tuple is provided as a cursor, it should be automatically converted to a list internally.
- Query cursors should be validated against the query's defined ordering, and a clear error should be raised if the cursor and ordering are incompatible (e.g., the cursor provides more values than there are order fields, or references fields not in the ordering).
- The ordering must be defined before cursor positions are applied; the system should enforce this and raise a descriptive error if a cursor is set without a corresponding ordering.
- When converting a dict-style cursor to the internal format, the values should be extracted in the same order as the query's order fields.

## Why This Matters

Supporting list/tuple cursors gives developers a simpler and more flexible way to paginate through results when they already know the ordered field values. The validation improvements ensure that ordering and cursor specification remain consistent, preventing silent bugs where a cursor applied before ordering would produce unexpected query results.
