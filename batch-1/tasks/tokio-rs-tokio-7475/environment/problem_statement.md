## Description

When building async applications that support graceful shutdown or cooperative cancellation, there is currently no ergonomic way to race an arbitrary future against a cancellation signal using a fluent call chain. Developers must hand-write the racing logic themselves each time, which is repetitive and error-prone.

## Expected Behavior

- Any future should be wrappable with a cancellation token in a single chained method call, without needing to manually write racing logic.
- When the inner future completes normally, the result should carry the produced value.
- When the cancellation signal fires before or during the future's execution, the result should indicate that the operation was cancelled rather than completed.
- When the cancellation signal has already been set before the future is polled for the first time, the future should resolve immediately to indicate cancellation — without spurious notifications to the async runtime.
- When cancellation fires while the future is pending, the async runtime should be notified exactly once so the future can be re-polled.
- The wrapper should be biased toward the inner computation: if both the inner future completes and the cancellation token fires at the same time, the inner future's result takes priority.
- Both borrowed and owned forms of the cancellation token should be supported.

## Why This Matters

This makes it much easier to integrate cancellation tokens into async code using fluent call chains, reducing boilerplate and making the intent clearer. It mirrors the ergonomics of similar adapter methods already available for timeouts.
