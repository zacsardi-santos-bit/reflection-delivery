Implement fixes for the retry status and cancellation bugs in the streaming UI to ensure accurate and consistent display of status messages and hints. Address issues with the retry utility to prevent callbacks from firing when operations have been aborted.

*   Update the `LoadingIndicator` component in `packages/cli/src/ui/components/LoadingIndicator.tsx`:
    *   Render the cancel-and-timer hint only when `streamingState` is `StreamingState.Responding`.
    *   Display the `currentLoadingPhrase` prop value when `streamingState` is `Idle`, but do not show the cancel-and-timer hint.

*   Modify the `useLoadingIndicator` function in `packages/cli/src/ui/hooks/useLoadingIndicator.ts`:
    *   Return `undefined` for `currentLoadingPhrase` when `streamingState` is `Idle`, regardless of `retryStatus`.
    *   Compute and return retry status phrases only when `streamingState` is `StreamingState.Responding`.

*   Adjust the `useGeminiStream` function in `packages/cli/src/ui/hooks/useGeminiStream.ts`:
    *   Ignore `CoreEvent.RetryAttempt` events when `isResponding` is `false`; ensure `retryStatus` remains `null`.
    *   Ensure `cancelOngoingRequest` immediately sets `retryStatus` to `null` upon cancellation.
    *   Prevent late `CoreEvent.RetryAttempt` events from updating `retryStatus` after cancellation.

*   Refine the `retryWithBackoff` function in `packages/core/src/utils/retry.ts`:
    *   Check the abort signal before executing the `onRetry` callback in the error-catch retry path.
        *   If `signal.aborted` is `true`, reject with an `AbortError` without calling `onRetry` or logging a warning.
    *   After `shouldRetryOnContent` returns `true`, check the abort signal.
        *   If `signal.aborted` is `true`, reject with an `AbortError` without calling `onRetry` or logging a warning.
    *   Ensure `shouldRetryOnContent` and the main function are each called exactly once in this scenario.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.