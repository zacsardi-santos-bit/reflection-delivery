## Description

Two separate maintenance cleanups are needed in the repository:

1. **Remove an unnecessary query option from the coverage exporter**: The CI script that exports code coverage data passes a row-read limit option to the database client. This option is no longer needed for this use case and should be removed from the client options string.

2. **Rename a fragile integration test helper**: An integration test helper function that polls until a sufficient number of refresh cycles have completed was originally implemented by tracking a maximum timestamp value from the batch log. This approach was fragile — when the dependency cycle re-ran a wave, duplicate rows could appear without advancing the timestamp, making the check unreliable. The function has been reworked to simply count rows, which is simpler and more robust. The function should be renamed to reflect its new row-counting behavior rather than its old timestamp-tracking identity. All usages of the old name must also be updated.

## Expected Behavior

- The coverage exporter script no longer references the now-unnecessary row-read limit option.
- The integration test helper function has a name that reflects counting rows, and the old timestamp-based name no longer appears anywhere in that test file.

## Why This Matters

These changes remove a deprecated option from the CI pipeline (preventing potential errors if the server no longer accepts it) and make the integration test helper less fragile by switching from a timestamp-frontier approach to a straightforward row count.
