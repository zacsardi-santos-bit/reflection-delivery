Implement distributed tracing for asynchronous RPC calls in the SOFA-RPC plugin by propagating the trace context from the initiating thread to the callback thread. Ensure that the callback processing appears as a linked segment in the distributed trace and that any errors during callback execution are logged.

Requirements:

* Implement the `InvokeCallbackWrapper` class:
    * Constructor must accept an `InvokeCallback` and store it.
    * `getInvokeCallback()` must return the stored `InvokeCallback`.
    * If constructed with no active trace span, `getContextSnapshot()` must return null.
    * If constructed with an active trace span, `getContextSnapshot()` must return a non-null `ContextSnapshot` with:
        * Trace ID matching the current global trace ID.
        * `getParentEndpoint()` matching the current span name.
    * `onResponse(Object)` must:
        * Create a local span.
        * Continue the captured context snapshot into the current thread.
        * Invoke the original callback's `onResponse`.
        * Stop the span.
        * Ensure the callback segment has exactly 1 span and matches the original span name.
    * `onException(Throwable)` must:
        * Create a local span.
        * Continue the captured context snapshot.
        * Log the throwable on the active span.
        * Invoke the original callback's `onException`.
        * Stop the span.
        * Ensure the callback trace segment has exactly 1 span with 1 log entry for the exception.

* Implement the `SofaBoltCallbackInvokeInterceptor` class:
    * `beforeMethod()` must:
        * Check if `allArguments[2]` is an instance of `InvokeCallback`.
        * If true, replace `allArguments[2]` with a new `InvokeCallbackWrapper` wrapping the original `InvokeCallback`.
        * Ensure `getInvokeCallback()` on the wrapper returns the original callback.
        * Leave `allArguments[2]` unchanged if it is not an instance of `InvokeCallback`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.