Implement a new lint rule in the ruff linter to detect overly specific return type annotations on iterator and async iterator methods within class bodies. The rule should identify when these methods are annotated with generator types instead of the simpler iterator types, and suggest using the latter. Ensure that the rule is applied only to class methods and not to module-level functions or methods with complex behaviors.

Requirements:

*   Implement a new lint rule with code PYI058 in the `flake8_pyi` rules category.
    *   Register the rule in the Preview rule group and name it `GeneratorReturnFromIterMethod`.
*   Flag `__iter__` methods in classes with:
    *   A bare `Generator` return annotation, suggesting `Iterator` instead.
    *   A `Generator[YieldType, SendType, ReturnType]` return annotation where `SendType` and `ReturnType` are `Any` or `None`, suggesting `Iterator[YieldType]`.
*   Flag `__aiter__` methods in classes with:
    *   A bare `AsyncGenerator` return annotation, suggesting `AsyncIterator` instead.
    *   An `AsyncGenerator[YieldType, SendType]` return annotation where `SendType` is `Any` or `None`, suggesting `AsyncIterator[YieldType]`.
*   Use diagnostic messages:
    *   For `__iter__`: "Use `Iterator` as the return value for simple `__iter__` methods."
    *   For `__aiter__`: "Use `AsyncIterator` as the return value for simple `__aiter__` methods."
*   Report diagnostics on the function name identifier.
*   Do not flag:
    *   Module-level `__iter__` or `__aiter__` functions.
    *   `__iter__` methods with non-trivial `SendType` or `ReturnType`.
    *   `__aiter__` methods with non-trivial `SendType`.
    *   `async def __aiter__` methods.
    *   Methods with extra parameters beyond `self`.
    *   In `.py` files, `__iter__` methods with non-trivial generator bodies.
*   Recognize `Generator` and `AsyncGenerator` from `collections.abc`, `typing`, or `typing_extensions`.
*   Create snapshot files for diagnostics:
    *   `ruff_linter__rules__flake8_pyi__tests__PYI058_PYI058.py.snap`
    *   `ruff_linter__rules__flake8_pyi__tests__PYI058_PYI058.pyi.snap`
*   Implement the rule in `bad_generator_return_type.rs` and export it from the `flake8_pyi` rules module.
*   Register the rule in:
    *   `crates/ruff_linter/src/codes.rs`
    *   `crates/ruff_linter/src/rules/flake8_pyi/mod.rs`
    *   `crates/ruff_linter/src/checkers/ast/analyze/statement.rs`
    *   `ruff.schema.json`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.