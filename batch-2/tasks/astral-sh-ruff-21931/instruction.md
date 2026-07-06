I'm getting false positive warnings from the lint rule that flags generator functions combining a yield expression with a value-bearing return statement.

*   The B901 lint rule (return_in_generator) must not emit a violation for a generator function whose decorator list contains a call to pytest.hookimpl with the keyword argument wrapper=True.

*   The B901 lint rule must not emit a violation for a generator function whose decorator list contains a call to pytest.hookimpl with the keyword argument hookwrapper=True.

*   The B901 lint rule must still emit a violation for a generator function decorated with a plain pytest.hookimpl() call that has no wrapper or hookwrapper argument.

*   The B901 lint rule must still emit a violation for a generator function decorated with pytest.hookimpl(wrapper=False).

*   The B901 lint rule must still emit a violation for a generator function decorated with pytest.fixture().

*   The snapshot file at crates/ruff_linter/src/rules/flake8_bugbear/snapshots/ruff_linter__rules__flake8_bugbear__tests__B901_B901.py.snap must be updated to include exactly three new B901 violations for the updated B901.py fixture file: one at line 116 (the pytest_configure function), one at line 122 (the pytest_unconfigure function), and one at line 128 (the my_fixture function). No violations must be present for the pytest_runtest_makereport (line 97), pytest_fixture_setup (line 104), or pytest_runtest_call (line 110) functions.


*   Interface details: Type: Rust source file (rule implementation)
Name: return_in_generator
Location: crates/ruff_linter/src/rules/flake8_bugbear/rules/return_in_generator.rs
Description: The B901 lint rule implementation. The public function `return_in_generator(checker: &Checker, function_def: &StmtFunctionDef)` must be updated to skip emitting a violation when the function's decorator list contains a pytest.hookimpl call with wrapper=True or hookwrapper=True. It should continue to emit violations for all other generator functions that use both yield and a value-bearing return.

Type: Rust source file (helper)
Name: is_pytest_hookimpl_wrapper
Location: crates/ruff_linter/src/rules/flake8_pytest_style/helpers.rs
Signature: is_pytest_hookimpl_wrapper(decorator: &Decorator, semantic: &SemanticModel) -> bool
Description: A helper function that returns true if the given decorator is a call to pytest.hookimpl with either wrapper=True or hookwrapper=True as a keyword argument. Returns false for pytest.hookimpl() with no arguments, pytest.hookimpl(wrapper=False), pytest.hookimpl(hookwrapper=False), and any non-hookimpl decorator. This function must be made pub(crate) so it can be imported from outside the flake8_pytest_style module.

Type: Rust module declaration
Name: helpers (flake8_pytest_style)
Location: crates/ruff_linter/src/rules/flake8_pytest_style/mod.rs
Description: The helpers module must be declared as pub(crate) (rather than the default mod-private) to allow return_in_generator.rs to import is_pytest_hookimpl_wrapper from it.

Type: Snapshot file
Name: ruff_linter__rules__flake8_bugbear__tests__B901_B901.py.snap
Location: crates/ruff_linter/src/rules/flake8_bugbear/snapshots/ruff_linter__rules__flake8_bugbear__tests__B901_B901.py.snap
Description: The insta snapshot file for the B901 rule tests. Must be updated to append exactly three new violation entries corresponding to the new test cases in B901.py. Violations must appear at B901.py line 116 (inside pytest_configure), line 122 (inside pytest_unconfigure), and line 128 (inside my_fixture). No violation entries must be added for lines 97, 104, or 110 (the hookimpl wrapper cases). Each entry uses the standard ruff diagnostic format with the violation message "Using `yield` and `return {value}` in a generator function can lead to confusing behavior", a source pointer, and the highlighted return statement.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.