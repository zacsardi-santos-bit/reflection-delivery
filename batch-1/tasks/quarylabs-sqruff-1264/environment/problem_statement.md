## Description

The PostgreSQL dialect does not support parsing the statement used to alter aggregate functions. This is a standard PostgreSQL DDL command used to rename an aggregate function, change its owner, or move it to a different schema. When a PostgreSQL file contains any of these statements, the parser currently fails to recognize the construct, making it impossible to lint or format such files.

## Expected Behavior

- Statements that alter aggregate functions should be recognized and parsed as valid PostgreSQL DDL.
- Renaming an aggregate to a new name should parse correctly, including when the aggregate takes multiple arguments.
- Changing the owner to a specific role — whether by name or using special system role identifiers — should parse correctly.
- Moving an aggregate to a different schema should parse correctly.
- The wildcard argument form (where the argument is a star/asterisk rather than a named type) should also be supported.

Additionally, the parser should correctly handle all standard join syntax variants in the PostgreSQL dialect — left/right/full outer joins, cross joins, and comma-separated table lists in the FROM clause.

## Why This Matters

PostgreSQL users commonly write migration scripts and schema management code that includes aggregate alterations. Without support for this statement, sqruff cannot process such files at all, blocking adoption for any project that manages custom aggregate functions.
