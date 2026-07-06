Implement support for formatting arrow function expressions and the type-check operator within Angular HTML templates using prettier. Ensure that these elements are formatted consistently with the rest of the template according to the specified options.

*   Format arrow function expressions in Angular templates:
    *   Normalize extra whitespace and apply consistent style in @let variable declarations, template interpolations ({{ }}), and event binding attributes.
    *   Respect the arrowParens option:
        *   If set to 'avoid', remove parentheses from single-parameter arrow functions.
        *   If set to 'always', add parentheses to single-parameter arrow functions in event bindings if they are missing.
    *   Split parameter lists across multiple lines when they exceed the printWidth limit, placing each parameter on its own indented line.
    *   Do not add trailing commas to arrow function parameter lists, regardless of the trailingComma option setting.

*   Format the instanceof binary operator in Angular template interpolations:
    *   Remove redundant parentheses and normalize internal whitespace.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.