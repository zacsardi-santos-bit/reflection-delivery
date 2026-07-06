## Description

Tests that interact with the database are not properly isolated from one another. When one test creates tables or other schema objects, those objects persist and can affect subsequent tests, making the test suite brittle and hard to debug. There is currently no standard utility to clean up database state between tests or to ensure that separately loaded module instances each get their own fresh database.

## Expected Behavior

- A utility should be available to close all active test database connections. After it runs and the module cache is cleared, re-importing the database module should produce a completely fresh, isolated database with no leftover schema or data from any previous instance.
- A utility should be available to reset a given database client back to a clean state by dropping all user-created tables, views, and triggers (but leaving indexes untouched). SQL identifiers that contain special characters such as double-quotes must be escaped correctly. After all schema objects are dropped, foreign key enforcement must be re-enabled.
- The database module itself must participate in this tracking: each new database connection created during testing should be registered so that the cleanup utilities know which connections to close.

## Why This Matters

Without proper isolation, a single test that creates a table can silently cause unrelated tests to fail or pass for the wrong reason. These utilities allow test infrastructure to reliably tear down and reset database state, making the suite deterministic and easier to maintain.
