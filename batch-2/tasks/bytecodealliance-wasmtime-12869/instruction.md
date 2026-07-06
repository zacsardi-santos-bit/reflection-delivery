I'm working in the Wasmtime/Pulley codebase and I need to make the Pulley virtual machine's constructor fallible.

*   The Vm::new() function must be changed to return a fallible result (Result<Self, OutOfMemory>) instead of returning the Vm directly, so that callers can handle initialization failures without panicking.

*   The Vm::with_stack(stack_size: usize) function must similarly be made fallible, returning Result<Self, OutOfMemory>, since stack memory allocation can fail for large or invalid sizes.

*   All existing call sites of Vm::new() in the codebase must be updated to call .unwrap() or otherwise handle the Result — including the callers in the Pulley integration tests and the Cranelift file test runner.

*   A new fallible store constructor (Store::try_new) must be provided that accepts an engine reference and data value and returns a Result, allowing callers to handle allocation failures without panicking.

*   When the maximum wasm stack or async stack size is configured to the largest possible value (usize::MAX), the runtime must not panic — store creation should either fail with an error (via try_new returning Err) or succeed and allow wasm execution.

*   Stack limit computation when entering wasm must not panic or overflow when the configured stack size is very large (including usize::MAX); the result should be a valid memory address rather than an arithmetic trap.


*   Interface details: Type: Function
Name: Vm::new
Location: pulley/src/interp.rs
Signature: fn new() -> Result<Self, OutOfMemory>
Description: Creates a new Pulley virtual machine with the default stack size. Must return a fallible Result so callers can handle initialization failures (e.g., out-of-memory during stack allocation). Previously returned Self directly; all callers must now call .unwrap() or propagate the error.

Type: Function
Name: Vm::with_stack
Location: pulley/src/interp.rs
Signature: fn with_stack(stack_size: usize) -> Result<Self, OutOfMemory>
Description: Creates a new Pulley virtual machine with the given stack size. Must be fallible to handle large or infeasible stack sizes gracefully.

Type: Function
Name: Store::try_new
Location: crates/wasmtime/src/runtime/store.rs
Signature: fn try_new(engine: &Engine, data: T) -> Result<Store<T>, impl Error>
Description: A new fallible constructor for a Wasmtime Store. Returns Err if resource allocation (e.g., stack allocation) fails, instead of panicking. This allows callers to handle cases where extreme configuration values (e.g., usize::MAX as the stack size) cause allocation failures.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.