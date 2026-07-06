Implement a fix for the TypeScript namespace transformer to ensure that namespace-local import alias declarations are correctly handled during transformation. Preserve these aliases as variable declarations when they are used as values, and remove them if they are only used in type annotations or not used at all, unless the transformer is configured to preserve all non-type imports.

*   Update the transformer to convert namespace-local import alias declarations from 'import X = Y.Z' to 'var X = Y.Z' in the transformed JavaScript output if the alias is used as a value within the namespace.
*   Remove namespace-local import alias declarations from the output if they are only referenced in type-annotation positions or not referenced at all, unless the 'onlyRemoveTypeImports' option is enabled.
*   Ensure that when the 'onlyRemoveTypeImports' option is enabled, all namespace-local import alias declarations are retained as 'var' variable declarations in the output, regardless of their usage.
*   Strip type annotations from variable declarations within the namespace in the transformed output, regardless of the preservation status of the associated import alias.
*   Implement changes in the file located at 'crates/oxc_transformer/src/typescript/namespace.rs'.
    *   Update the TypeScriptNamespace struct to store the `only_remove_type_imports` boolean option from `TypeScriptOptions`.
    *   Use symbol reference information to determine whether to retain or remove namespace-local import alias declarations based on their usage and the `only_remove_type_imports` setting.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.