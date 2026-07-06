## Description

The SQL parser currently represents LIKE, NOT LIKE, ILIKE, NOT ILIKE, and SIMILAR TO pattern-matching expressions as generic binary operations in the abstract syntax tree. This design has a significant limitation: there is no place in a binary operator node to store the optional escape character that is part of the SQL standard syntax for these expressions. As a result, SQL that uses pattern-matching expressions with an escape clause cannot be correctly parsed or represented.

There is also a related issue with how the PostgreSQL-specific tilde-based symbolic operator variants for pattern matching are handled. These are currently being normalized to their keyword equivalents during formatting, which means that SQL using operator syntax does not round-trip correctly — you put in one form and get a different form out.

## Expected Behavior

- LIKE / NOT LIKE expressions should be represented by a dedicated AST node that carries the subject expression, a negation flag, the pattern expression, and an optional escape character.
- ILIKE / NOT ILIKE expressions should have the same dedicated representation.
- SIMILAR TO / NOT SIMILAR TO expressions should also have their own dedicated AST node with the same structure.
- When an escape clause is present in any of these expressions, the escape character must be preserved in the AST and round-tripped correctly.
- The PostgreSQL tilde-based symbolic operator variants should be treated as distinct operators from the SQL keywords and must preserve their original symbolic form when formatted back to SQL.

## Why This Matters

Without this change, any query that uses pattern-matching with an escape character will silently lose information when parsed. This can lead to incorrect query semantics downstream. Additionally, tools that parse and re-emit SQL will inadvertently change the syntax of PostgreSQL-style pattern-matching operators, which may be surprising and incorrect.
