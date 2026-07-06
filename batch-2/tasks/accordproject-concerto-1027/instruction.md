Implement a function to enforce naming conventions in Concerto model files using a linter. Ensure the linter identifies and reports naming violations for type declarations, properties, and enum constants according to specified naming rules.

*   Implement the `lintAST` function with the following signature:
    *   Location: `packages/concerto-linter/src/index.ts`
    *   Signature: `lintAST(ast: string): Promise<Array<any>>`
    *   Description: Accepts a JSON-serialized Concerto model AST string and returns a Promise that resolves to an array of lint violation objects.

*   Type Declarations:
    *   Ensure all type declarations (concept, asset, participant, transaction, event, enum, scalar) use camelCase.
    *   Flag each type declaration whose name begins with an uppercase letter as a violation.
    *   Return one violation per offending declaration name.

*   Properties (Fields) in Non-Enum Types:
    *   Ensure all properties use PascalCase.
    *   Do not flag properties that are correctly named.
    *   Flag each property whose name does not begin with an uppercase letter as a violation.

*   Enum Constants:
    *   Ensure all enum constants use UPPER_SNAKE_CASE.
    *   Flag each constant that does not follow this format as a violation.
    *   Return one violation per offending constant name.

*   Return an empty array when no naming violations are detected in the model.

*   Export `lintAST` as a named export from `packages/concerto-linter/src/index.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.