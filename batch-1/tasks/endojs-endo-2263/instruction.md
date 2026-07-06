Implement a utility that allows generator-based logic to be executed in both synchronous and asynchronous contexts. Create two driver functions, `syncTrampoline` and `asyncTrampoline`, to handle the execution of the generator with appropriate callbacks and error handling.

*   Implement `syncTrampoline` in `packages/trampoline/src/trampoline.js`:
    *   Accept a generator function, a synchronous thunk callback, and any additional arguments.
    *   Drive the generator synchronously by calling `next()` with the return value of each yielded thunk call.
    *   Return the generator's final return value.
    *   Propagate errors thrown by the thunk back into the generator using `throw()`.
    *   Support recursive generator delegation via `yield*`.

*   Implement `asyncTrampoline` in `packages/trampoline/src/trampoline.js`:
    *   Accept a generator function, a synchronous or Promise-returning thunk callback, and any additional arguments.
    *   Drive the generator asynchronously by awaiting each yielded value and calling `next()` with the resolved result.
    *   Return a Promise of the generator's final return value.
    *   Propagate rejections or errors from the thunk back into the generator using `throw()`.
    *   Support recursive generator delegation via `yield*`.

*   Export both `syncTrampoline` and `asyncTrampoline` as named exports from `packages/trampoline/src/trampoline.js`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.