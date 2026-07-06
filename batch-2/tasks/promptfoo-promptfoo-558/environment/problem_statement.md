## Description

After recent changes that introduced database persistence to promptfoo, the entire test suite for core evaluation features has started failing. Tests for assertions, evaluation logic, provider integrations, test-case loading, and utility functions all fail when the test runner imports the relevant source modules — not because of any issue with those modules themselves, but because importing them now triggers database initialization that requires a real database to be running.

## Expected Behavior

- Tests for assertions, evaluator, providers, test cases, and utilities should all run and pass without requiring a live database connection.
- Database initialization logic should be isolated in its own module so that it can be cleanly disabled during testing.
- Mocking the database layer in tests should prevent any database-related side effects from interfering with unrelated test logic.

## Why This Matters

Developers should be able to run the full test suite in a local environment without needing to set up and configure a database. The current situation blocks CI and local development workflows because a foundational infrastructure concern (database connectivity) is leaking into tests that have nothing to do with persistence.
