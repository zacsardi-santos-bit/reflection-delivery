Update the OpAMP protocol library dependency in the operator bridge component to the latest version that supports new protocol capabilities. Ensure the project compiles successfully and all existing tests pass after the update.

*   Upgrade the opamp-go library dependency in the `go.mod` file:
    *   Change the version from v0.12.0 to v0.14.0.
*   Update the `go.sum` file to include the necessary checksum entries for opamp-go v0.14.0:
    *   Include the checksum `h1:KoziIK+wsFojhUXNTkCSTnCPf0eCMqFAaccOs0HrWIY=`.
    *   Include the go.mod hash `XOGCigljsLSTZ8FfLwvat0M1QDj3conIIgRa77BWrKs=`.
*   Ensure the entire operator-opamp-bridge agent package compiles successfully after the dependency upgrade.
*   Verify that all existing agent tests pass, including:
    *   Health checking tests.
    *   Message handling tests.
    *   Identity update tests.
    *   Collector key operation tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.