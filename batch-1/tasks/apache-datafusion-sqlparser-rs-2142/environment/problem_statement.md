## Description

The SQL parser does not support the PostgreSQL-style syntax for setting a user's password via an alter-user statement. Specifically, these valid SQL forms fail to parse:

- Setting a password to a string literal
- Setting a password to null (to clear it)
- Marking the password as encrypted
- Using an optional leading qualifier keyword before the password clause

This is a common pattern in PostgreSQL and compatible databases, so tools and libraries that need to parse or round-trip SQL user management statements are blocked from supporting it.

## Expected Behavior

- Setting a user's password to a literal string value should parse successfully and round-trip back to the same output
- Setting an encrypted password to a literal string value should parse successfully and preserve the encrypted marker in the output
- Setting a user's password to null (to clear it) should parse successfully and round-trip back to the null form in output
- Using the optional qualifier keyword before the password clause should parse successfully and be normalized in output to the equivalent form without that qualifier

## Why This Matters

Database administrators and developers building SQL tooling for PostgreSQL-compatible systems frequently use these forms of the alter-user statement to manage credentials. Without parser support, these valid SQL statements produce parse errors and cannot be used in round-trip parsing workflows.
