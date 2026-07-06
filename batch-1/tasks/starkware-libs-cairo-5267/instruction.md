Refactor the Sierra code generator to accumulate statements within the generator context, eliminating the need for code generation functions to return statement lists. Implement methods in the context to manage statement accumulation and location information consistently.

*   Update `generate_block_code`:
    *   Change return type to `Maybe<()>`.
    *   Accumulate statements in `ExprGeneratorContext` instead of returning them.

*   Modify `ExprGeneratorContext`:
    *   Implement `statements(self) -> Vec<pre_sierra::StatementWithLocation>` to return accumulated statements.
    *   Implement `push_statement(&mut self, statement: pre_sierra::Statement)` to wrap statements with the current location and append them.
    *   Implement `maybe_set_cairo_location(&mut self, location: Option<StableLocation>)` to update the current location if provided.
    *   Add fields `curr_cairo_location: Option<StableLocation>` and `statements: Vec<pre_sierra::StatementWithLocation>`.

*   Adjust `ExprGeneratorContext::new_label`:
    *   Change return type to `(pre_sierra::Statement, pre_sierra::LabelId)`.

*   Update utility functions in `crates/cairo-lang-sierra-generator/src/utils.rs`:
    *   `jump_statement` and `return_statement` should return `pre_sierra::Statement`.
    *   Rename `simple_statement` to `simple_basic_statement` to return `pre_sierra::Statement`.
    *   Provide a backward-compatible `simple_statement` wrapper.

*   Refactor internal code generation functions:
    *   Change return type to `Maybe<()>` and use `push_statement` and `maybe_set_cairo_location`.
    *   Functions include `generate_block_body_code`, `generate_statement_code`, `generate_return_code`, `add_drop_statements`, and match/struct/enum/snapshot helpers.

*   Update `generate_match_code`:
    *   Remove `statements` and `statement_cairo_location` parameters.
    *   Set location on the context before calling.
    *   Change `arm_labels` type to `Vec<(pre_sierra::Statement, pre_sierra::LabelId)>`.

*   Modify `maybe_add_dup_statements` and `maybe_add_dup_statement`:
    *   Remove `statements` parameter.
    *   Push dup statements directly into the context.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.