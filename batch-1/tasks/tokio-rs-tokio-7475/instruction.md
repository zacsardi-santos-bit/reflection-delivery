Implement a convenient way to race any future against a cancellation token using a fluent extension method. Add an extension trait on futures that provides two methods to handle cancellation tokens, making the integration into async code more ergonomic and reducing boilerplate.

*   Implement the `FutureExt` trait in `tokio-util/src/future.rs`.
    *   Ensure it is publicly accessible at `tokio_util::future::FutureExt`.
    *   Provide a blanket implementation for all types implementing `Future`.
*   Define the `with_cancellation_token` method.
    *   Accept a borrowed `CancellationToken`.
    *   Return a new `Future` with an output type of `Option<T>`, where `T` is the original future's output.
    *   Ensure that if the cancellation token is not cancelled, a ready future polls to `Poll::Ready(Some(value))` and a pending future to `Poll::Pending`.
    *   If the cancellation token is already cancelled at the first poll, resolve immediately to `Poll::Ready(None)` without invoking the waker.
*   Define the `with_cancellation_token_owned` method.
    *   Accept an owned `CancellationToken`.
    *   Return a new `Future` with an output type of `Option<T>`, where `T` is the original future's output.
*   Ensure both methods handle cancellation correctly:
    *   If a pending future is wrapped with a non-cancelled token, and the token is cancelled after polling, notify the waker exactly once. The next poll should return `Poll::Ready(None)`.
    *   Prioritize the inner future: if both the inner future becomes `Ready` and the token is cancelled before the poll, return `Poll::Ready(Some(value))`.
    *   Invoke the waker once when the token is cancelled, even if the inner future takes priority.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.