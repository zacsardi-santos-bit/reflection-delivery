Implement support for exporting a function as the entry point in Pulumi programs, allowing for asynchronous operations during initialization. Ensure the runtime detects and correctly processes exported functions, handling both resource registration and stack outputs.

*   Detect when a Pulumi program's module exports a callable function and invoke it with no arguments.
*   Await the result of the function, regardless of its type:
    *   Regular synchronous function
    *   Synchronous function returning a Promise
    *   Async function
*   Deeply resolve any nested asynchronous values in the function's return value before registering them as stack outputs.
    *   Example: A return value of `{ a: Promise.resolve({ x: Promise.resolve(99), y: 'z' }), b: 42, c: { d: 'a', e: false } }` should produce stack outputs `{ a: { x: 99, y: 'z' }, b: 42, c: { d: 'a', e: false } }`.
*   Ensure resources created inside the exported function are tracked and registered as stack resources, just as if they were created at the module's top level.
*   Register stack outputs as an empty object `{}` if the exported function returns no value (undefined or nothing).
*   Correctly handle scenarios where the exported function both creates resources and returns a value:
    *   Register created resources with the stack.
    *   Use the return value as stack outputs.
*   Implement these changes in the language host's program execution path in `sdk/nodejs/cmd/run/run.ts`, where the user program is loaded via `require()` and its result is passed to the stack runner.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.