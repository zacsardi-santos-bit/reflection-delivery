Implement a builder method on the `OptimizationConfig` type to configure a minimal set of moveable functions for testing. Ensure this method can be used in a chain with other configuration methods to produce consistent and predictable test outputs.

*   Implement the `with_minimal_movable_functions` method in the `OptimizationConfig` type.
    *   The method signature must be `pub fn with_minimal_movable_functions(self) -> Self`.
    *   Define this method in the `impl` block of `OptimizationConfig` located in `crates/cairo-lang-lowering/src/optimizations/config.rs`.
    *   Ensure the method is publicly accessible.

*   Configure the method to restrict moveable functions to a minimal set.
    *   The minimal set must include only the function `felt252_sub`.
    *   Internally, call `with_moveable_functions` with a list containing only "felt252_sub".

*   Ensure the method follows the builder pattern.
    *   The method must take ownership of `self` and return `Self`.
    *   Allow chaining with other builder methods, such that `OptimizationConfig::default().with_minimal_movable_functions()` compiles and returns a valid `OptimizationConfig`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.