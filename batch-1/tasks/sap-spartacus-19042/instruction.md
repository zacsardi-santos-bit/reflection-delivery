Implement a custom ESLint rule to ensure that any NgRx action class with "Fail" in its name implements the `ErrorAction` interface from `@spartacus/core`. Create utility functions to facilitate checking and modifying class interface implementations and import statements.

*   Update the module at `tools/eslint-rules/rules/no-ngrx-fail-action-without-error-action-implementation.ts`:
    *   Export a string constant named `RULE_NAME`.
    *   Export an ESLint `rule` object that:
        *   Flags classes with "Fail" in their name missing the `ErrorAction` interface using `missingImplementsErrorAction`.
        *   Auto-fixes by adding `implements ErrorAction` to the class and `import { ErrorAction } from '@spartacus/core';` to the file.
        *   Does not flag classes already implementing `ErrorAction` or those without "Fail" in their name.

*   Implement utility functions in `tools/eslint-rules/rules/utils/implements-interface-utils.ts`:
    *   `hasImplementsInterface(node: TSESTree.ClassDeclaration, interfaceName: string): boolean`
        *   Return true if the class implements the specified interface anywhere in its list.
        *   Return false if the class does not implement the specified interface.
    *   `fixMissingImplementsInterface(params: { fixer: RuleFixer, interfaceName: string, node: TSESTree.ClassDeclaration, sourceCode: SourceCode }): RuleFix`
        *   Insert `implements InterfaceName` after the class name if no `implements` or `extends` clauses exist.
        *   Insert `implements InterfaceName` after the superclass if no `implements` clause exists.
        *   Append `, InterfaceName` to the existing implements list.

*   Implement utility functions in `tools/eslint-rules/rules/utils/import-utils.ts`:
    *   `isIdentifierImported(params: { importedIdentifier: string, importPath: string, sourceCode: SourceCode }): boolean`
        *   Return true if the identifier is already imported from the specified module.
    *   `fixPossiblyMissingImport(params: { fixer: RuleFixer, importedIdentifier: string, importPath: string, sourceCode: SourceCode }): RuleFix`
        *   Insert `import { Identifier } from 'path';` at the top if no imports exist.
        *   Insert `import { Identifier } from 'path';` after the last import with a blank line if imports exist.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.