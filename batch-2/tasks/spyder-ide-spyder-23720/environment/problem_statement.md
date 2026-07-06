## Description

When writing tests for Spyder plugins, each plugin's test directory has historically contained its own copy of the same boilerplate: a mock main window, plugin registration/teardown helpers, and session-level setup. This duplication makes it tedious to add new plugin tests and keeps improvements from propagating everywhere.

A recent refactoring effort reorganized this shared setup into a single centralized test-utilities location so all plugin test suites can simply import from one place. The test configuration files in the IPython console plugin's test directory were already updated to reference this new shared location — but the shared module itself was never created.

As a result, whenever pytest tries to collect tests in that directory, the configuration file fails to load with a module-not-found error. This blocks every test in the directory from running, including ones that have nothing to do with the shared plugin infrastructure (such as the kernel connection-dialog tests, which were passing before the refactoring).

## Expected Behavior

- A shared test-utilities module for Spyder plugins should exist and be importable.
- The module should provide a session-scoped mock main window fixture that is compatible with Spyder's plugin registration system.
- The module should provide an abstract session-scoped fixture that callers override with their list of plugin classes to register for the test session.
- The module should provide an autouse session-scoped fixture that reads the plugin list and dynamically creates session fixtures for each plugin, handling registration on setup and unregistration on teardown.
- Tests that do not use any of the dynamic plugin fixtures (like the connection-dialog tests) should continue to pass without any plugin setup occurring.

## Why This Matters

The missing module causes the entire IPython console widget tests directory to fail at collection time, making it impossible to run previously passing tests. Creating the module restores normal test collection and keeps the shared infrastructure usable by multiple plugin test suites.
