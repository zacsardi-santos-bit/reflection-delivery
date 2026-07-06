I'm working on improving database test isolation in our project.

*   The closeTestDatabaseClients function must close all database clients that were registered during testing, and after it completes the global client registry must be empty.

*   When closeTestDatabaseClients is called followed by a module cache reset, re-importing the database module and calling getDb() must return a new, fully isolated database instance that does not contain schema objects or data from any prior module instance.

*   The resetTestDatabaseClient function must accept a client object exposing an execute method and a close method, query the client for all user-created schema objects, and drop every object whose type is 'table', 'view', or 'trigger'.

*   resetTestDatabaseClient must NOT issue a DROP statement for schema objects whose type is 'index'.

*   resetTestDatabaseClient must escape SQL identifiers by wrapping each name in double-quotes and replacing any double-quote character within the name with two consecutive double-quote characters. For example, a table named table"name must be dropped as: DROP TABLE IF EXISTS "table""name".

*   The DROP statements issued by resetTestDatabaseClient must use the form 'DROP TABLE IF EXISTS "name"', 'DROP VIEW IF EXISTS "name"', and 'DROP TRIGGER IF EXISTS "name"' with upper-case SQL keywords.

*   The last SQL statement executed by resetTestDatabaseClient must be exactly 'PRAGMA foreign_keys = ON', even if errors occur while dropping schema objects.

*   The getDb function in src/database/index.ts must register each newly created database client with registerTestDatabaseClient when the IS_TESTING environment variable is set, so that closeTestDatabaseClients can later close it.


*   Interface details: Type: Function
Name: closeTestDatabaseClients
Location: src/database/testing.ts
Signature: closeTestDatabaseClients() -> Promise<void>
Description: Closes all registered test database clients and clears the global registry. After calling this and resetting module caches, re-importing the database module produces a new, isolated database instance with no shared schema or data from prior instances.

Type: Function
Name: resetTestDatabaseClient
Location: src/database/testing.ts
Signature: resetTestDatabaseClient(client: { close: () => any; execute: (sql: string) => Promise<{ rows: Array<{ type: string; name: string }> }> }) -> Promise<void>
Description: Resets the schema of a given database client by dropping all user-created tables, views, and triggers (but NOT indexes). Identifiers are escaped by wrapping in double-quotes and doubling any internal double-quote characters. The final SQL executed must be exactly 'PRAGMA foreign_keys = ON'.

Type: Function
Name: registerTestDatabaseClient
Location: src/database/testing.ts
Signature: registerTestDatabaseClient(client: { close: () => any; execute: (sql: string) => Promise<any> }) -> void
Description: Registers a database client in the global test registry so that closeTestDatabaseClients() is aware of it and can close it. Must be called from getDb() in src/database/index.ts when the IS_TESTING environment variable is set, so that module-level isolation works correctly.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.