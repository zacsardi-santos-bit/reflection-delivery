## Description

Several services in this codebase rely on an older Redis client library that works through an explicit connection-pool abstraction. A newer, more modern Redis client library is now available that provides a simpler, context-aware API with automatic connection management. We need to migrate all Redis interactions to use this newer library.

The middleware package currently fails to compile because its test file already references the newer Redis client library while the implementation still depends on the older one.

## Expected Behavior

- The rate limiting middleware should be updated to accept the newer Redis client type instead of a connection pool. The extra database index parameter is no longer needed and should be removed.
- The cryptocurrency price fetching client should be updated so it accepts and uses the newer Redis client type for cache operations.
- The ratios service should be updated to accept and use the newer Redis client type internally.
- All Redis operations (reads, writes, sorted set queries) throughout these components should use the context-aware API provided by the newer client.

## Why This Matters

Currently the middleware package fails to compile at all due to the mismatched dependency. This blocks running any tests in the package. Completing the migration will restore compilation, allow tests to pass, and leave the codebase on a consistent, modern Redis client throughout.

## Additional Notes

Some time-sensitive credential filtering tests use the current system time for comparisons, which means they will begin failing as real-world dates pass the hardcoded expiry values in the test data. These test cases should use fixed reference dates to stay deterministic.
