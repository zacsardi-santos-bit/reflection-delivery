## Description

Currently, the network request timeout used for outgoing connections (including proxy connections) is hardcoded and cannot be changed without a code deployment. There is no way for operators to configure a different timeout value at runtime through the experiment flag system, which makes it difficult to tune request behavior for different environments or network conditions.

Additionally, if the language model client is accessed before the application has finished fetching its remote configuration, the error message shown to developers is unclear about what initialization step was skipped.

## Expected Behavior

- Operators should be able to configure a default request timeout (in seconds) via a remote experiment feature flag. The application should pick up this value and apply it to all outgoing HTTP requests — including those routed through a proxy — converting seconds to milliseconds automatically.
- Proxy connections should respect the experiment-driven timeout rather than always using a fixed hardcoded value.
- If the language model client is accessed before experiments have been fetched, the system should throw a clear error indicating that experiments must be fetched first, distinct from the existing error about authentication not being complete.

## Why This Matters

Without a configurable timeout, it is impossible to tune request behavior for slow networks or strict SLA environments without a code change. The new guard on client access also makes initialization failures much easier to diagnose during development and integration testing.
