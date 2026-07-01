Implement a configurable maximum cap on the primary hedging delay for secondary dispatch systems in SpiceDB. Refactor the hedging sleep logic into a dedicated, self-contained component that supports cancellation and is safe for concurrent use.

*   Update the `SecondaryDispatch` struct:
    *   Add a new field `MaximumPrimaryHedgingDelay` of type `time.Duration`.
    *   Ensure it is located in `internal/dispatch/remote/cluster.go`.

*   Modify the `getPrimaryWaitTime` method:
    *   Accept an additional parameter `maximumDelay` of type `time.Duration`.
    *   Cap the returned wait time by `maximumDelay`.

*   Update the `getWaitTime` method:
    *   Accept a `maximumDelay` parameter of type `time.Duration`.
    *   Return `startingPrimaryHedgingDelay` if the internal digest count is below the minimum.
    *   Return the quantile-based value capped by `maximumDelay` when count meets or exceeds the minimum.

*   Implement behavior for `SecondaryDispatch` with `MaximumPrimaryHedgingDelay` set to 0:
    *   Ensure no hedging delay is applied to the primary dispatcher.
    *   Use the primary even on the first request, reflecting the primary's dispatch count.

*   Define the `primarySleeper` struct in `internal/dispatch/remote/primarysleeper.go`:
    *   Include fields: `reqKey` (string), `waitTime` (time.Duration), `cancelFunc` (func()), and `lock` (sync.Mutex).
    *   Ensure it is instantiatable with struct literal syntax.

*   Implement the `primarySleeper.sleep` method:
    *   Block for `waitTime` duration when positive.
    *   Return in under 1ms when `waitTime` is zero.
    *   Return early if the context is cancelled or its deadline expires.
    *   Return in under 5ms if the context deadline has already passed.
    *   Ensure concurrent calls independently wait the full `waitTime`.
    *   Observe metrics during execution when `reqKey` is 'check'.

*   Implement the `primarySleeper.cancelSleep` method:
    *   Cause an ongoing sleep call to return early.
    *   Ensure calling before sleep begins has no effect on the subsequent sleep.
    *   Ensure it is safe to call multiple times and from multiple concurrent goroutines.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.