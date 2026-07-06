Implement variant inference for built-in types in the Gleam compiler to allow more precise pattern matching without unnecessary branches. Update the compiler to recognize constant values of built-in types and infer their variants correctly.

*   Implement the `bool_with_variant` function in `compiler-core/src/type_/prelude.rs`.
    *   Make it publicly accessible from the `type_` module.
    *   Accept an `Option<bool>` argument.
    *   Return a Bool named type with `inferred_variant` set to:
        *   `Some(0)` for `Some(true)`.
        *   `Some(1)` for `Some(false)`.
        *   `None` for `None`.

*   Update the existing `bool()` function:
    *   Delegate its functionality to `bool_with_variant(None)` to maintain existing behavior.

*   Modify the prelude constructors:
    *   Set the `True` constructor return type to use `bool_with_variant(Some(true))` (inferred_variant index 0).
    *   Set the `False` constructor return type to use `bool_with_variant(Some(false))` (inferred_variant index 1).
    *   Set the `Ok` constructor return type to have `inferred_variant` set to `Some(0)`.
    *   Set the `Error` constructor return type to have `inferred_variant` set to `Some(1)`.

*   Ensure pattern matching behavior:
    *   Allow pattern matching on a value always constructed as the `Ok` variant of `Result` to compile successfully with only `Ok` patterns and a wildcard, without requiring an `Error` branch.
    *   Allow pattern matching on the literal value `True` to be exhaustive with only a `True` branch, without requiring a wildcard or `False` branch.

*   Update the language server 'add missing patterns' code action:
    *   Ensure it correctly generates missing patterns for a `Bool` function parameter in an empty case expression, adding `False -> todo` and `True -> todo` branches.
    *   Ensure it works correctly when the case subject is a typed function parameter (e.g., `Bool` or `#(Bool, Result(Int, Nil))`), without relying on variant information from let-bound literal values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.