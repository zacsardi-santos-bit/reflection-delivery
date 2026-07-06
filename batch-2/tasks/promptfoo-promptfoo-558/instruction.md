Isolate the database initialization logic into a dedicated module to enable mocking during tests. Ensure that tests for assertions, evaluation, providers, test-case loading, and utilities run without requiring a live database connection.

*   Create a dedicated database module at `src/database.ts`.
    *   Encapsulate all database initialization and connection logic within this module.
    *   Ensure the module is structured to allow Jest's automatic module mocking during tests.

*   Update source modules to import database functionality from the `src/database` module.
    *   Ensure utility functions, evaluation, providers, test-case loading, and assertions modules import from `src/database` rather than initializing databases inline.

*   Mock the database module in tests to prevent database-related side effects.
    *   Ensure all existing tests for assertions, evaluation, providers, test-case loading, and utility functions pass without requiring a real database connection.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.