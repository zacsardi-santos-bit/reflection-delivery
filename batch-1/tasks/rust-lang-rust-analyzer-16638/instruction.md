Implement a new refactoring assist in rust-analyzer to convert plain struct variable bindings into destructuring patterns. Ensure the assist is triggered when the cursor is on a struct variable name, and update all field accesses accordingly.

*   Register a new IDE refactoring assist with the identifier "destructure_struct_binding" in the assists system.
*   Implement the assist in a new handler file:
    *   Create the file at `crates/ide-assists/src/handlers/destructure_struct_binding.rs`.
    *   Add the handler to the list in `crates/ide-assists/src/lib.rs`.
*   Ensure the assist activates when the cursor is on a variable name of a struct binding.
    *   Replace the variable pattern with a struct destructuring pattern that names all visible fields using the struct's type path.
    *   Use shorthand destructuring syntax for record structs where field names do not conflict with other names in scope.
*   Update all subsequent field access expressions on the original binding variable within the same scope to use the destructured field names.
*   Preserve reference operators in field access expressions:
    *   When a field access is wrapped in a reference expression, maintain the reference operator after substituting the field name.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.