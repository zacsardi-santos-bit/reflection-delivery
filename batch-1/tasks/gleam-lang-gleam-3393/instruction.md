Update the Gleam compiler to ensure that when an inexhaustive case expression error is reported, the missing patterns are displayed using the names and qualifiers that are valid and expected in the developer's current scope. This involves adjusting the error message generation to respect module aliases, constructor aliases, unqualified imports, and any shadowing.

Requirements:

*   Implement logic to ensure missing patterns in error messages use the correct scope-specific names:
    *   Use the same name and qualifier as the user would in their source code.
    *   For qualified imports, use the module-qualified form (e.g., 'module.Constructor').
    *   For module aliases, use the alias as the qualifier (e.g., 'alias.Constructor').
    *   For unqualified imports, display the constructor name without a module qualifier.
    *   For constructors imported with a local alias, use the alias instead of the original name.
    *   For built-in prelude constructors used unqualified, display them without a module qualifier.
    *   For explicitly imported prelude modules, use the 'gleam.' qualifier.
    *   For shadowed types, use the module-qualified form to disambiguate.
    *   For constructors imported under an alias, use the alias if it's the only unambiguous reference.

*   Format the exhaustiveness error message as follows:
    *   Start with 'error: Inexhaustive patterns' header.
    *   Include the source file location and caret underline of the case expression.
    *   Add explanatory text: 'This case expression does not have a pattern for all possible values. If it is run on one of the values without a pattern then it will crash.'
    *   List missing patterns under 'The missing patterns are:' section, with each pattern indented by four spaces.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.