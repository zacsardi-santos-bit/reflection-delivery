Implement a mechanism to distinguish between root cause failures and concurrent failures in Flink job failure handling. Ensure that only root cause failures increment the restart counter and that concurrent failures are grouped under the root cause in the exception history.

*   Update the `notifyFailure(Throwable)` method in the `RestartBackoffTimeStrategy` interface:
    *   Change the return type to `boolean`.
    *   Return `true` if the failure is the first in a new restart attempt, `false` if a restart is already pending.

*   Modify the `ExponentialDelayRestartBackoffTimeStrategy` class:
    *   Implement the updated `notifyFailure` method.
    *   Return `false` if `now <= nextRestartTimestamp`, otherwise return `true`.

*   Update other restart strategy implementations (`FixedDelayRestartBackoffTimeStrategy`, `NoRestartBackoffTimeStrategy`, `FailureRateRestartBackoffTimeStrategy`):
    *   Implement the updated `notifyFailure` method to always return `true`.

*   Adjust the `FailureHandlingResult` class:
    *   Add a `boolean isNewAttempt` parameter to both `restartable` and `unrecoverable` factory methods.
    *   Implement a new `isRootCause()` method that returns the value of `isNewAttempt`.

*   Modify the `ExecutionFailureHandler`:
    *   Pass `true` as `isNewAttempt` for non-recoverable failures caused by suppressed-restarts exceptions.
    *   Increment `numberOfRestarts` only when `notifyFailure` returns `true`.

*   Update the `FailureHandlingResultSnapshot` class:
    *   Add a `boolean isRootCause` parameter to the @VisibleForTesting constructor.
    *   Implement a new `isRootCause()` method.
    *   Ensure the `create` factory method derives `isRootCause` from `FailureHandlingResult.isRootCause()`.

*   Enhance the `RootExceptionHistoryEntry` class:
    *   Add a `addConcurrentExceptions(Iterable<Execution>)` method.
    *   Ensure the internal concurrent exceptions collection is mutable to allow appending entries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.