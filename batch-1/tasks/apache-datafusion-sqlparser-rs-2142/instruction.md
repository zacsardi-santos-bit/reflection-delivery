Enhance the SQL parser to support PostgreSQL-style syntax for altering a user's password. Ensure it can handle setting passwords to string literals, null, and encrypted forms, and normalize optional qualifiers in the output.

*   Implement support for parsing ALTER USER statements with a PASSWORD clause:
    *   Accept literal string values (e.g., PASSWORD 'somestring') and ensure the parsed Abstract Syntax Tree (AST) re-emits the same form.
    *   Accept ENCRYPTED PASSWORD with a literal string value, preserving the ENCRYPTED keyword in the output.
    *   Accept PASSWORD NULL and ensure the output remains PASSWORD NULL, not an empty string.
*   Implement handling for the optional WITH keyword:
    *   Accept the WITH keyword immediately after the user name in ALTER USER statements (e.g., ALTER USER u1 WITH PASSWORD 'AAA').
    *   Normalize the output to exclude WITH, treating it as equivalent to its omission (e.g., ALTER USER u1 PASSWORD 'AAA').

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.