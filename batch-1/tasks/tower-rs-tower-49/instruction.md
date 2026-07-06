Implement the `InFlightLimit` middleware to manage and limit the number of concurrent requests to a service. Ensure it handles capacity release scenarios correctly and supports multiple clones sharing the same capacity pool.

*   Implement the `InFlightLimit` struct in `tower-in-flight-limit/src/lib.rs`:
    *   Use the constructor `InFlightLimit::new(inner: T, max: usize) -> InFlightLimit<T>` to initialize with an inner service and a maximum concurrency limit.
    *   Ensure `InFlightLimit` implements `Clone`, with all clones sharing the same capacity pool.

*   Manage request capacity:
    *   Implement `poll_ready()` to return `Async::NotReady` when the number of in-flight requests equals the configured maximum, preventing additional requests.
    *   Ensure `poll_ready()` reserves capacity correctly and allows `call()` to forward requests to the inner service when capacity is available.
    *   If `call()` is invoked without a prior successful `poll_ready()` and the service is at capacity, return a response future that resolves to the `NoCapacity` variant of the `Error` enum.

*   Handle capacity release:
    *   Release the in-flight capacity slot when a response future completes, whether successfully or with an error.
    *   Release the capacity slot when a response future is dropped before completion.
    *   Release the reserved capacity slot when a cloned service instance is dropped before sending a request.

*   Special case handling:
    *   With a maximum concurrency of zero, ensure `poll_ready()` always returns `Async::NotReady` and any `call()` immediately returns an error future.

*   Implement the `Error` enum in `tower-in-flight-limit/src/lib.rs`:
    *   Include the `NoCapacity` variant for requests made without available capacity.
    *   Include the `Upstream(T)` variant to wrap errors from the inner service.

*   Implement the `ResponseFuture` struct in `tower-in-flight-limit/src/lib.rs`:
    *   Ensure it releases the in-flight capacity slot when it completes or is dropped before completion.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.