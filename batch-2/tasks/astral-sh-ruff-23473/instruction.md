I noticed that the lint rule which warns about inefficient dictionary iteration only catches the pattern in traditional for-loops — but the same problem can appear in comprehensions and generator expressions, and the linter stays completely silent there.

*   The PERF102 rule must detect violations in list comprehensions, set comprehensions, dict comprehensions, and generator expressions where a dictionary's items() is iterated with a two-element tuple target but only one element is used in the body.

*   When the comprehension generator's tuple target has only the key used in the comprehension body (value is a wildcard or unused named variable), the rule must flag the use of items() and suggest replacing it with keys().

*   When the comprehension generator's tuple target has only the value used in the comprehension body (key is a wildcard or unused named variable), the rule must flag the use of items() and suggest replacing it with values().

*   The rule must NOT flag a comprehension generator when both elements of the tuple target are referenced in the comprehension body.

*   The rule must NOT flag a comprehension generator when the loop target is not a two-element tuple unpacking (e.g., when the target is a single variable).

*   The rule must NOT flag a comprehension generator when one of the tuple target elements is used in a filter (if) condition within the same comprehension clause.

*   For comprehensions with multiple generators (nested iteration), each generator that iterates over dict.items() should be checked independently for the violation.

*   The comprehension detection must be activated only when preview mode is enabled; the existing for-loop detection should remain active regardless of preview mode.

*   A new test case for the IncorrectDictIterator rule with the PERF102.py fixture must be registered in the preview_rules test section in crates/ruff_linter/src/rules/perflint/mod.rs.

*   The existing non-preview snapshot for PERF102 must be updated to reflect the extended fixture file (additional blank lines at end of file context). The snapshot file is at crates/ruff_linter/src/rules/perflint/snapshots/ruff_linter__rules__perflint__tests__PERF102_PERF102.py.snap.

*   A new preview-mode snapshot file must be created at crates/ruff_linter/src/rules/perflint/snapshots/ruff_linter__rules__perflint__tests__preview__PERF102_PERF102.py.snap containing diagnostics for all PERF102 violations including the new comprehension violations.


*   Interface details: Type: Function
Name: incorrect_dict_iterator_comprehension
Location: crates/ruff_linter/src/rules/perflint/rules/incorrect_dict_iterator.rs
Signature: pub(crate) fn incorrect_dict_iterator_comprehension(checker: &Checker, comprehension: &ast::Comprehension)
Description: Checks a single generator clause within a comprehension or generator expression for PERF102 violations. Applies the same logic as the for-loop checker: if the generator iterates over dict.items() with a two-element tuple target and only one element is used in the enclosing comprehension, it emits a diagnostic suggesting keys() or values() instead. This function is the comprehension-context counterpart to the existing incorrect_dict_iterator function.

Type: Function
Name: is_incorrect_dict_iterator_comprehension_enabled
Location: crates/ruff_linter/src/preview.rs
Signature: pub(crate) const fn is_incorrect_dict_iterator_comprehension_enabled(settings: &LinterSettings) -> bool
Description: Preview gate that controls whether PERF102 detection is active for comprehensions and generator expressions. Returns true when preview mode is enabled in the linter settings. Must be checked before calling incorrect_dict_iterator_comprehension.

---

## Test Registration

In `crates/ruff_linter/src/rules/perflint/mod.rs`, a test case must be added to the **preview_rules** test section:

```
#[test_case(Rule::IncorrectDictIterator, Path::new("PERF102.py"))]
```

This registers the snapshot test that validates comprehension detection under preview mode.

---

## Snapshot Files

**Updated (non-preview):** `crates/ruff_linter/src/rules/perflint/snapshots/ruff_linter__rules__perflint__tests__PERF102_PERF102.py.snap`
- This file must be updated to reflect that the fixture file now has additional lines at the end (the blank separator lines before the new comprehension section). The change adds two blank-line entries at the end of the existing snapshot content.

**New (preview):** `crates/ruff_linter/src/rules/perflint/snapshots/ruff_linter__rules__perflint__tests__preview__PERF102_PERF102.py.snap`
- A new snapshot file that must be created containing diagnostics for all PERF102 violations detected when running in preview mode on PERF102.py. This includes all existing for-loop violations plus the new comprehension violations from lines 111–117 of the fixture.
- The new comprehension violations appear at lines 111, 112, 113, 114, 115, 116, and 117 of the fixture file.
- Each violation follows the same message format as the existing PERF102 rule: "When using only the keys of a dict use the `keys()` method" (with fix "Replace `.items()` with `.keys()`") or "When using only the values of a dict use the `values()` method" (with fix "Replace `.items()` with `.values()`").
- All fixes are marked as unsafe ("This is an unsafe fix and may change runtime behavior").

---

## Deferred Comprehension Analysis Infrastructure

To call `incorrect_dict_iterator_comprehension` at the correct point in analysis (after the comprehension's scope is fully resolved), the AST checker must defer comprehension analysis and process it after for-loop deferred analysis:

- `crates/ruff_linter/src/checkers/ast/deferred.rs`: Add a `comprehensions: Vec<Snapshot>` field to the `Analyze` struct.
- `crates/ruff_linter/src/checkers/ast/analyze/deferred_comprehensions.rs`: New module that drains `checker.analyze.comprehensions`, restores each snapshot, extracts the generator list from the current comprehension expression, and calls `incorrect_dict_iterator_comprehension` for each generator when `Rule::IncorrectDictIterator` is enabled.
- `crates/ruff_linter/src/checkers/ast/analyze/mod.rs`: Export and declare the new `deferred_comprehensions` module.
- `crates/ruff_linter/src/checkers/ast/mod.rs`: When visiting a comprehension expression and `Rule::IncorrectDictIterator` is enabled and the preview gate returns true, push `self.semantic.snapshot()` onto `self.analyze.comprehensions`. After deferred for-loop analysis, call `analyze::deferred_comprehensions(&mut checker)`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.