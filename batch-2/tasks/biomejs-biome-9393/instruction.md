I'd like to add a new lint rule to the JavaScript analyzer that enforces placing lifecycle hooks before test cases within the same block.

*   A new lint rule named `useTestHooksOnTop` must be implemented under the `lint/nursery` group in the JavaScript analyzer.

*   The rule must flag any lifecycle hook (`beforeAll`, `beforeEach`, `afterEach`, `afterAll`, `before`, `after`) that appears after a test case (`it`, `test`) within the same scope (a `describe` block or the top level of the file).

*   Each hook that violates the ordering rule must produce a separate diagnostic, even if multiple hooks follow the same test case.

*   The diagnostic primary message must read: `Lifecycle hook {hookName} appears after a test case.` where `{hookName}` is the actual hook function name.

*   The diagnostic must include an informational note: `Placing the hook after this test case makes it harder to spot the setup and teardown for these tests at a glance.`

*   The diagnostic must include a suggestion: `Move the hook above all test cases in the same block for better readability.`

*   Each `describe` block constitutes its own independent scope for the rule; a hook that appears before all tests in an outer scope does not affect evaluation of an inner scope, and vice versa.

*   A hook that appears before any test in its scope must NOT be flagged; only hooks that appear after at least one test case are invalid.

*   The rule must also apply at the top level of a file (outside any `describe` block): a hook appearing after a top-level `it` or `test` call must be flagged.

*   Nested violations inside a `describe`-within-a-`describe` must be detected and flagged even when the traversal entry point is the outer describe.

*   When a block contains a hook before a test (valid) followed by another hook after a test (invalid), only the later hook is flagged — not the earlier valid one.

*   The rule must belong to the `nursery` group and include the standard nursery group disclaimer in all diagnostics.

*   The rule implementation file must be placed at `crates/biome_js_analyze/src/lint/nursery/use_test_hooks_on_top.rs` and registered in the nursery group.


*   Interface details: Type: Lint Rule
Name: useTestHooksOnTop
Location: crates/biome_js_analyze/src/lint/nursery/use_test_hooks_on_top.rs
Description: A new lint rule that flags lifecycle hooks appearing after test cases in the same scope. Must be implemented as a Biome JS lint rule struct and registered in the nursery group at `crates/biome_js_analyze/src/lint/nursery/mod.rs` (or the equivalent nursery group registry file).

Rule category path: lint/nursery/useTestHooksOnTop

Recognized lifecycle hook names (functions that trigger the rule when placed after a test):
- beforeAll
- beforeEach
- afterEach
- afterAll
- before
- after

Recognized test case names (functions that, once seen, cause any subsequent hook in the same scope to be flagged):
- it
- test

Diagnostic messages (exact strings required by the snapshot tests):
- Primary message: "Lifecycle hook {hookName} appears after a test case."
  (where {hookName} is the literal name of the misplaced hook, e.g. "beforeEach")
- Informational note: "Placing the hook after this test case makes it harder to spot the setup and teardown for these tests at a glance."
- Advice/suggestion: "Move the hook above all test cases in the same block for better readability."

Test spec files (must already exist in the repo for the snapshot runner to pick them up):
- crates/biome_js_analyze/tests/specs/nursery/useTestHooksOnTop/invalid.js
- crates/biome_js_analyze/tests/specs/nursery/useTestHooksOnTop/invalid.js.snap
- crates/biome_js_analyze/tests/specs/nursery/useTestHooksOnTop/valid.js
- crates/biome_js_analyze/tests/specs/nursery/useTestHooksOnTop/valid.js.snap


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.