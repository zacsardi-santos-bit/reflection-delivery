## Description

When a worker-scoped fixture teardown throws an error, the reporter's error callback is invoked without any worker context. This means reporters cannot determine which worker or project was responsible for the error, making it impossible to produce meaningful, contextualized error reports for fixture teardown failures.

## Expected Behavior

- When a worker-scoped fixture teardown fails (throws an error), the reporter's error handler should receive not only the error itself, but also the worker context associated with that error.
- The worker context should identify which worker was running (as a numeric index), which parallel slot it occupied (also a numeric index), and which project it belonged to.
- The error message itself should be accessible and contain the text of the thrown error.

## Why This Matters

Reporters are expected to provide full diagnostic context for all errors during a test run. When worker fixture teardowns fail, the current behavior provides no way to associate those errors with a specific worker or project configuration, making it difficult to debug failures in distributed or multi-project test suites. Passing worker context alongside the error enables reporters to give users complete, actionable error reports.
