Implement a feature in the Gleam compiler to detect and warn when the same module is imported multiple times in a single source file, even if different aliases are used. Ensure the warning is clear and points to all relevant import locations.

*   Emit a warning when a module is imported more than once in a single file.
    *   Ensure the warning title is 'Duplicate import'.
    *   Format the warning message as 'The {module_name} module has been imported twice.' using the full module path from the import statements.
    *   Highlight both import statement locations in the diagnostic output.
*   Ensure the compiler correctly identifies and processes all import statements to detect duplicates.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.