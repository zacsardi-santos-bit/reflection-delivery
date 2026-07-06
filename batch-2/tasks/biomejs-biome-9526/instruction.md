I have a Svelte component that imports a utility function and uses it in two places: once in the script block to extract its parameter types for a TypeScript type annotation, and once directly in the template as a called function inside an HTML attribute expression.

*   The useImportType lint rule must not generate any diagnostic for a Svelte file where a named import is used both as a type (e.g., via 'typeof' inside a generic utility type in the script block) and as a runtime value (e.g., called as a function in a template attribute expression like class={fn(arg)}).

*   When a Svelte template attribute uses a text/expression initializer that calls an imported symbol as a function, the rule must recognize that symbol as a runtime value reference and not a type-only reference.

*   The rule must handle the case where an import appears in a type-only position (such as 'typeof importedSymbol' within a generic type parameter in the script block) alongside a value-position usage in the template, and must produce zero diagnostics for such an import.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.