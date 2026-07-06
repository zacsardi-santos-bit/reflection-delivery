I'm working with a linter that detects redundant dictionary membership checks — the pattern where you first test if a key is in a dictionary, and then access the dictionary with the same key in the same boolean expression.

*   A new tristate type must be introduced to represent whether an expression definitely has no side effects, may have side effects, or definitely has side effects. This type must support merging two values (definite wins over possible wins over absent) and querying each state.

*   A new public helper function must traverse an expression tree and return the combined side-effect classification. F-strings that interpolate non-literal expressions (anything other than numeric constants, boolean literals, None, ellipsis, string literals, or bytes literals) must be classified as 'possibly side-effectful'. Named expressions (walrus operator) must be classified as 'definitely side-effectful'. Function calls, subscripts, await, yield, and comprehensions must be classified as 'definitely side-effectful'. All other expressions must be classified as absent of side effects.

*   The RUF019 rule must use the new tristate side-effect classification instead of the previous boolean check. When the combined side-effect of the object and key expressions is 'definitely present', the rule must return without emitting any diagnostic.

*   When the combined side-effect is 'possibly present' (e.g. the key is an f-string interpolating a non-literal), the RUF019 rule must still emit a diagnostic but must classify the automatic fix as unsafe.

*   When the combined side-effect is absent and no comments intersect the expression, the automatic fix must be classified as safe.

*   The snapshot file at crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__RUF019_RUF019.py.snap must be updated to include: an unsafe-fix diagnostic for `if f"{c}" in d and d[f"{c}"]` (non-literal f-string), a safe-fix diagnostic for `if f"{1}" in d and d[f"{1}"]` (literal f-string), a safe-fix diagnostic for `if f"key" in d and d[f"key"]` (plain f-string), and no diagnostic for `if (k := "key") in d and d[(k := "key")]` (walrus operator).


*   Interface details: The implementation requires changes in two Rust files and updating one snapshot file.

---

Type: Enum
Name: SideEffect
Location: crates/ruff_python_ast/src/helpers.rs
Description: A tristate enum representing whether an expression has no side effects, may have side effects, or is assumed to have side effects. Must be public (`pub`).
Variants:
  - Absent  — the expression is definitely side-effect-free
  - Possible — the expression may have side effects (e.g. f-string interpolation may invoke `__format__` or `__str__`)
  - Present  — the expression is assumed to have definite side effects (e.g. walrus operator, function call)
Methods that must be present:
  - `is_present(self) -> bool`  — returns true only for Present
  - `is_absent(self) -> bool`   — returns true only for Absent
  - `merge(self, other: Self) -> Self` — combines two SideEffect values: Present wins over Possible wins over Absent

---

Type: Function
Name: side_effect
Location: crates/ruff_python_ast/src/helpers.rs
Signature: `pub fn side_effect<F>(expr: &Expr, is_builtin: F) -> SideEffect where F: Fn(&str) -> bool`
Description: Traverses an expression and returns the combined SideEffect of all sub-expressions. An f-string (ExprFString) that contains any interpolated element whose expression is not a literal (number, bool, None, ellipsis, string, bytes) must return SideEffect::Possible. A named expression (walrus operator, Expr::Named) must return SideEffect::Present. Unknown/complex expressions such as function calls, subscripts, await, yield, comprehensions, etc. return SideEffect::Present. Literal expressions, names, attribute accesses, and simple operators return SideEffect::Absent. The function short-circuits and returns SideEffect::Present as soon as a Present sub-expression is found.

---

Type: Rule Implementation
Name: unnecessary_key_check (RUF019)
Location: crates/ruff_linter/src/rules/ruff/rules/unnecessary_key_check.rs
Description: The existing rule that detects `key in dict and dict[key]` patterns must be updated:
  - Replace the call to `contains_effect` with calls to the new `side_effect` function for both the object and key sub-expressions.
  - Merge the two resulting SideEffect values.
  - If the combined effect is Present (definite side effects), return early without emitting a diagnostic.
  - If the combined effect is NOT Absent (i.e., Possible), OR if comments intersect the expression range, classify the auto-fix as Unsafe.
  - Otherwise (combined effect is Absent and no comments), classify the auto-fix as Safe.

---

Type: Snapshot File
Name: RUF019_RUF019.py snapshot
Location: crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__RUF019_RUF019.py.snap
Description: The insta snapshot that the test comparing RUF019 linter output compares against. Must be updated to include the expected diagnostics for the new fixture cases added in crates/ruff_linter/resources/test/fixtures/ruff/RUF019.py:
  - A diagnostic at the line `if f"{c}" in d and d[f"{c}"]:` (non-literal f-string) with an UNSAFE fix, suggesting replacement with `d.get(f"{c}")`, including the "unsafe fix and may change runtime behavior" note.
  - A diagnostic at the line `if f"{1}" in d and d[f"{1}"]:` (literal f-string) with a SAFE fix, suggesting replacement with `d.get(f"{1}")`.
  - A diagnostic at the line `if f"key" in d and d[f"key"]:` (plain f-string) with a SAFE fix, suggesting replacement with `d.get(f"key")`.
  - No diagnostic for the line `if (k := "key") in d and d[(k := "key")]:` (walrus operator).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.