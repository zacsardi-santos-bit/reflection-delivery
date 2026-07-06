## Description

When the Hadoop configuration system reads sensitive values like passwords, it currently returns them as plain strings. This means that if the value is passed to a logger or accidentally interpolated into a log message, the actual secret appears in plain text in the logs. There is no way for a caller to tell — just from the type — that a value is sensitive and should be handled carefully.

## Expected Behavior

- Sensitive configuration values (passwords and credentials) should be returned wrapped in a dedicated type that signals their sensitivity at the API level.
- The actual underlying value should still be accessible when explicitly requested.
- When a sensitive configuration value is retrieved, the logging system should automatically record a redacted placeholder rather than the real value, so that secrets never appear in log output.
- Non-sensitive configuration values (like string collections) should continue to log their actual values normally.

## Why This Matters

Accidentally logging passwords and secrets is a common security problem. By encoding sensitivity in the type system and enforcing redaction at the point of retrieval, developers get protection against credential leakage by default — even when they aren't thinking about it. Any call site that previously received a raw string will also need to be updated to explicitly extract the underlying value when needed.
