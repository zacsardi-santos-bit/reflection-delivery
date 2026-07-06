I'm working on a Python language server and noticed that the code folding feature doesn't handle multiline block headers properly.

*   When a Python function definition has its parameters spread across multiple lines inside parentheses, the folding range response must include a fold region that starts immediately after the opening parenthesis and ends at the start of the closing parenthesis line.

*   When a Python function definition has a multiline header (parameters on separate lines), the fold region for the function body must start from the end of the last line of the header (after the colon), not from the start of the function keyword.

*   When a match statement body contains cases, the folding range response must include a fold region covering the entire match body, starting from the end of 'match <subject>:' and ending at the last character of the last case body.

*   When a match case has a pattern that spans multiple lines inside a delimiter pair (such as a dictionary pattern with braces), the folding range response must include a fold region starting immediately after the opening delimiter and ending at the start of the closing delimiter.

*   When a match case has a multiline pattern, the fold region for the case body must start from the end of the line containing the closing delimiter of the pattern (after the colon), not from the start of the 'case' keyword.

*   The folding range response must not emit separate expression fold regions for delimiter-bounded expressions that are already covered as part of a block header fold region, to avoid duplicate overlapping folds.


*   Interface details: The fix involves modifying internal logic in the folding range computation module located at `crates/ty_ide/src/folding_range.rs`. No new public API functions are introduced; the change updates the behavior of the existing folding range visitor that is already called by the public `folding_ranges` function.

A unit test named `test_folding_range_multiline_block_headers` must also be added to the `#[cfg(test)]` module inside `crates/ty_ide/src/folding_range.rs`. This test must cover multiline block headers for: function definitions (with parameters spanning multiple lines), function definitions with complex default values where both the default expression and the return type span multiple lines, class definitions with type parameters and base classes spanning multiple lines, if statements with multiline conditions, for statements with multiline targets and iterables, with statements with multiple context managers spanning multiple lines, and try/except blocks with multiline exception tuples. The test uses the `CursorTest` infrastructure (via `CursorTest::builder().source(...).build()`) and `assert_snapshot!` with an inline snapshot of the expected folding range output.

The following internal methods and structures are introduced or modified within `FoldingRangeVisitor` in `crates/ty_ide/src/folding_range.rs`:

Type: Method
Name: add_block_header_ranges
Location: crates/ty_ide/src/folding_range.rs
Signature: add_block_header_ranges(&mut self, parent: AnyNodeRef<'a>, header_range: TextRange)
Description: Scans the tokens within the header range for matching delimiter pairs (parentheses, brackets, braces). For each such pair that spans multiple lines, adds a folding range covering the content between the delimiters, and records it as an active block header delimiter range so that overlapping expression folds can be suppressed.

Type: Method
Name: add_block_ranges
Location: crates/ty_ide/src/folding_range.rs
Signature: add_block_ranges<T: Ranged>(&mut self, node: AnyNodeRef<'a>, block: &[T])
Description: Combines the block header fold (via add_block_header_ranges) and the block body fold (via add_block_body_range) for compound statements. The header range spans from the start of the function/class name (or the node start for other constructs) to the start of the first statement in the block.

Type: Method
Name: add_expression_range
Location: crates/ty_ide/src/folding_range.rs
Signature: add_expression_range(&mut self, range: TextRange)
Description: Adds a fold range for an expression node only if it does not intersect with any active block header delimiter range. This prevents duplicate fold regions when a multiline expression appears inside a block header.

Type: Method
Name: add_range
Location: crates/ty_ide/src/folding_range.rs
Signature: add_range(&mut self, folding_range: impl Into<FoldingRange>) -> bool
Description: Updated to return a bool indicating whether the range was actually added (true if multiline and within the range filter, false otherwise). Previously returned nothing.

Type: Struct
Name: ActiveBlockHeaderDelimiterRange
Location: crates/ty_ide/src/folding_range.rs
Description: Tracks a block header delimiter range that has been added as a fold, paired with its parent AST node reference. Used to suppress overlapping expression folds and cleaned up in leave_node when the parent node is exited.
Signature: struct ActiveBlockHeaderDelimiterRange<'a> { parent: AnyNodeRef<'a>, range: TextRange }

Type: Method
Name: leave_node (on SourceOrderVisitor impl)
Location: crates/ty_ide/src/folding_range.rs
Signature: leave_node(&mut self, node: AnyNodeRef<'a>)
Description: Pops all active block header delimiter ranges whose parent matches the node being left, keeping the active set clean during traversal.

Type: Method
Name: add_block_body_range_after_keyword
Location: crates/ty_ide/src/folding_range.rs
Signature: add_block_body_range_after_keyword(&mut self, parent: AnyNodeRef<'a>, keyword: TokenKind, previous_block_end: TextSize, block: &[T])
Description: Updated to accept a parent node reference (first parameter) and now also calls add_block_header_ranges for the keyword's header span before adding the body range.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.