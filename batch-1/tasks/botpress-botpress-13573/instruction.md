Implement a generic middleware pipeline utility in the `InterceptorManager` class to manage a sequence of handlers for processing values and errors. Ensure handlers can modify or clear errors, short-circuit execution, and respect cancellation signals.

*   Implement the `InterceptorManager` class in `packages/cognitive/src/interceptors.ts`.
    *   Make it a generic class accepting a type parameter for the value type.
*   Implement the `use` method to register interceptor handlers.
    *   Handlers must be executed in the order they are registered.
    *   Each handler receives four arguments: the current error (or null), the current value, a `next` callback, and a `done` callback.
*   Implement the `run` method to execute the chain of registered interceptors.
    *   Accepts a value and an `AbortSignal`.
    *   Returns a Promise resolving with the final value or rejecting with the final error.
    *   If the `AbortSignal` is aborted before or during execution, reject the promise with the abort reason.
*   Ensure handlers can:
    *   Use `next(err, val)` to pass control to the next handler with a modified error or value.
    *   Use `done(err, val)` to skip remaining handlers and end the chain.
    *   Receive and handle errors, potentially clearing them and continuing with `next(null, newValue)`.
*   Ensure the promise:
    *   Resolves with the final accumulated value if no error remains at the end.
    *   Rejects with the final error if it is not cleared by the end of the chain.
*   Ensure values passed through `next` accumulate, allowing each interceptor to transform the value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.