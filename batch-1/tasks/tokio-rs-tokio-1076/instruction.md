Implement a guard-based approach for entering spans in the tracing library to allow spans to remain active across multiple statements without using closures. Rename the existing closure-based span entry method for clarity and ensure both methods handle disabled spans as no-ops.

*   Update the `Span` type to include:
    *   `in_scope` method:
        *   Accepts a closure `FnOnce() -> T` and executes it within the span context.
        *   Enters the span before calling the closure and exits the span after the closure returns.
        *   Returns the closure's result of type `T`.
        *   Signature: `pub fn in_scope<F: FnOnce() -> T, T>(&self, f: F) -> T`
        *   Handles disabled spans by calling the closure without sending enter/exit notifications.
    *   `enter` method:
        *   Returns an `Entered` guard value.
        *   Keeps the span entered while the guard is live.
        *   Handles disabled spans as no-ops.
        *   Signature: `pub fn enter<'a>(&'a self) -> Entered<'a>`

*   Implement the `Entered` struct:
    *   Public guard representing an entered span.
    *   Holds a reference to the `Span` with lifetime `'a`.
    *   Exits the span by calling the subscriber's exit callback when dropped.
    *   Annotated with `#[must_use]`.
    *   Signature: `pub struct Entered<'a> { span: &'a Span }`

*   Ensure both methods produce identical subscriber notifications:
    *   `in_scope`: Enter on closure call, exit after closure returns.
    *   `enter`: Subscriber enter called on guard creation, exit called when guard is dropped.

*   Ensure the `in_scope` method propagates the return value of the closure, supporting non-unit types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.