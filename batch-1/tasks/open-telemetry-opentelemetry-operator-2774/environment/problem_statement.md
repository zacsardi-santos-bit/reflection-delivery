## Description

The operator bridge component depends on an OpAMP protocol library that has released a new version. The new version extends the protocol client interface with two additional capabilities: the ability to send custom messages and the ability to declare custom capabilities. The project needs to be updated to use this newer version of the library.

After the library is upgraded, the bridge agent's test mock (which simulates the OpAMP client) must implement the new interface methods so the code compiles. The test file already includes an updated mock with those method stubs and a compile-time interface verification — but the underlying library version in the project's module files has not yet been updated.

## Expected Behavior

- The project's dependency on the OpAMP client library should be updated to the newer version that includes custom message and custom capabilities support.
- Once the dependency is updated, the package should compile successfully.
- All existing agent tests (health checks, message handling, identity updates, collector key operations) should continue to pass.

## Why This Matters

Keeping up with new versions of the OpAMP protocol library ensures the operator bridge can take advantage of new protocol capabilities and stays compatible with OpAMP server implementations. Failing to update leaves the code pinned to an older interface and prevents the test suite from compiling when the mock is updated to match the new interface.
