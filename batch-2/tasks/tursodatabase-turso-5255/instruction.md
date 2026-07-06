I'm working with the JavaScript bindings in this repository and I've run into a problem with how SQL is split into individual statements before being sent to the database.

*   The splitStatements function must be exported from bindings/javascript/turso-sql-split.mjs and accept a SQL string, returning an array of individual statement strings.

*   Each statement string returned by splitStatements must have its surrounding whitespace trimmed, but internal whitespace (including newlines and indentation within the statement body) must be preserved exactly.

*   When the SQL input contains a CREATE TRIGGER statement with a BEGIN...END body (regardless of how many semicolon-terminated sub-statements appear inside), splitStatements must return the entire trigger definition — from CREATE TRIGGER through the closing END; — as a single element in the result array.

*   When the SQL input contains a trigger definition followed by additional statements (such as a SELECT), splitStatements must return the trigger as one element and each subsequent statement as its own element.

*   The END keyword appearing inside a single-quoted string literal within a trigger body must be ignored as a trigger terminator; only a bare END outside string literals signals the actual end of the trigger.

*   Both CREATE TEMP TRIGGER and EXPLAIN CREATE TRIGGER variants must be recognized as trigger definitions and their BEGIN...END bodies must be kept intact as a single statement.


*   Interface details: Type: Function
Name: splitStatements
Location: bindings/javascript/turso-sql-split.mjs
Signature: splitStatements(sql: string): string[]
Description: Splits a SQL string into an array of individual SQL statement strings. Each returned statement has its surrounding whitespace trimmed. Trigger definitions (CREATE TRIGGER, CREATE TEMP TRIGGER, EXPLAIN CREATE TRIGGER) with a BEGIN...END body are returned as a single element regardless of how many semicolon-terminated statements appear inside the body. The word END appearing inside a string literal within a trigger body is not treated as a trigger terminator.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.