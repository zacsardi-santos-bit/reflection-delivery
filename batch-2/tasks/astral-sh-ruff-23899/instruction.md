I'm working on a Python linter and I've noticed that the naming convention rules don't apply to variables bound inside pattern-matching statements.

*   The `non_lowercase_variable_in_function`, `mixed_case_variable_in_class_scope`, and `mixed_case_variable_in_global_scope` rule functions must accept a `TextRange` parameter instead of `&Expr` for the source location, so they can be invoked from pattern-analysis context as well as expression-analysis context.

*   A new `pattern` analysis function must be created in `crates/ruff_linter/src/checkers/ast/analyze/pattern.rs`. It must handle `PatternMatchAs` (when a name is present), `PatternMatchStar` (when a name is present), and `PatternMatchMapping` (when a rest name is present), and call the appropriate naming-convention check for the current scope.

*   The `pattern` function must be exported from the `analyze` module and called in the AST checker's pattern visitor (after the pattern subtree walk) so that every visited pattern node is analyzed.

*   In function scope, the naming-convention check must flag any match/case capture that binds a non-lowercase name, including: simple captures (`case BadName:`), captures inside class patterns (`case int(GoodName):`), star captures (`case [*Rest]:`), mapping value captures (`case {"key": Val}:`), and as-aliases (`... as Alias`). Each bound identifier is flagged individually at its own source position.

*   In class scope, the naming-convention check must flag mixed-case names bound by match/case patterns, including captures inside class patterns and both the capture name and the alias name when a pattern uses `as` (e.g., `case fN2 as fN3:` produces two separate violations).

*   At global/module scope, the naming-convention check must flag mixed-case names bound by match/case patterns, including both sides of an `as`-alias and mapping rest captures (`**name`).

*   Lowercase capture names and the wildcard pattern (`_`) must not be flagged by any of the three naming-convention rules.

*   The insta snapshot files for N806, N815, and N816 must be updated to include the new violations produced by the match/case fixture additions, with exact source positions and variable names matching the fixture files.


*   Interface details: Type: Function
Name: non_lowercase_variable_in_function
Location: crates/ruff_linter/src/rules/pep8_naming/rules/non_lowercase_variable_in_function.rs
Signature: non_lowercase_variable_in_function(checker: &Checker, range: TextRange, name: &str)
Description: Checks whether a variable name in a function scope is non-lowercase (N806 rule). Parameter changed from `expr: &Expr` to `range: TextRange` so it can be called from both expression and pattern analysis contexts.

Type: Function
Name: mixed_case_variable_in_class_scope
Location: crates/ruff_linter/src/rules/pep8_naming/rules/mixed_case_variable_in_class_scope.rs
Signature: mixed_case_variable_in_class_scope(checker: &Checker, range: TextRange, name: &str, class_def: &ast::StmtClassDef)
Description: Checks whether a variable name in a class scope is mixed-case (N815 rule). Parameter changed from `expr: &Expr` to `range: TextRange`.

Type: Function
Name: mixed_case_variable_in_global_scope
Location: crates/ruff_linter/src/rules/pep8_naming/rules/mixed_case_variable_in_global_scope.rs
Signature: mixed_case_variable_in_global_scope(checker: &Checker, range: TextRange, name: &str)
Description: Checks whether a variable name in global/module scope is mixed-case (N816 rule). Parameter changed from `expr: &Expr` to `range: TextRange`.

Type: Function
Name: pattern
Location: crates/ruff_linter/src/checkers/ast/analyze/pattern.rs
Signature: pattern(pattern: &Pattern, checker: &Checker)
Description: Entry point for running naming-convention lint rules over a Pattern syntax node. Must be exported from crates/ruff_linter/src/checkers/ast/analyze/mod.rs and called inside the Checker's visit_pattern implementation after walking the pattern (Step 4: Analysis).

Type: Snapshot file
Name: ruff_linter__rules__pep8_naming__tests__N806_N806.py.snap
Location: crates/ruff_linter/src/rules/pep8_naming/snapshots/ruff_linter__rules__pep8_naming__tests__N806_N806.py.snap
Description: Insta snapshot file that must be updated to include N806 violations for the match/case patterns added to the N806.py fixture: BadName (line 65), GoodName (line 67), Rest (line 69), Val (line 71), and Alias (line 71).

Type: Snapshot file
Name: ruff_linter__rules__pep8_naming__tests__N815_N815.py.snap
Location: crates/ruff_linter/src/rules/pep8_naming/snapshots/ruff_linter__rules__pep8_naming__tests__N815_N815.py.snap
Description: Insta snapshot file that must be updated to include N815 violations for the match/case patterns added to the N815.py fixture: tP (line 34), fN (line 36), fN2 (line 38), fN3 (line 38).

Type: Snapshot file
Name: ruff_linter__rules__pep8_naming__tests__N816_N816.py.snap
Location: crates/ruff_linter/src/rules/pep8_naming/snapshots/ruff_linter__rules__pep8_naming__tests__N816_N816.py.snap
Description: Insta snapshot file that must be updated to include N816 violations for the match/case patterns added to the N816.py fixture: tP (line 15), fN1 (line 17), fN2 (line 17), fN3 (line 19).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.