I've noticed that the Python formatter handles return type annotations inconsistently depending on whether the function has parameters or not. When a function has parameters, the formatter correctly expands a long generic return type inline — for example, it opens the bracket on the same line and puts elements on the next line without adding extra outer parentheses. But when the same function has no parameters at all, the formatter wraps the entire return type in extra outer parentheses, which diverges from how the reference formatter handles it.

I'd like the formatter to be improved so that:
- Functions with parameters never get extra outer parentheses around their return type annotations, even when the type is long — they should simply expand the brackets inline. This should work consistently across both stable and a new preview mode.
- Functions without parameters should parenthesize their return type in stable mode when it doesn't fit (to maintain existing behavior), but a new preview mode should make them behave just like functions with parameters — expanding inline without extra wrapping.
- List literals used as return type annotations should not be formatted with the tightly-grouped bracket style that is normally applied to collection arguments inside function calls.
- The formatting for trailing-comma subscript return types on no-parameter functions should match what the reference formatter produces, without adding unnecessary outer parentheses.

It would also be helpful to add dedicated test fixtures that clearly document the expected formatting behavior separately for functions with parameters and functions without parameters.
