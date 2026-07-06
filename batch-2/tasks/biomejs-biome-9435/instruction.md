I'm working with Astro files and running the linter with the rule that suggests converting regular imports to type-only imports.

*   When linting an Astro file with the useImportType rule enabled, a component import must NOT be flagged for conversion to a type-only import if that component is referenced as a JSX element in the Astro file's template section (the markup area after the closing frontmatter delimiter).

*   JSX component usage in the Astro template section (e.g., using an imported identifier as a rendered element tag) must be recognized as a value-position reference, preventing the useImportType rule from incorrectly suggesting the import be converted to a type-only import.

*   When the useImportType rule is applied to an Astro file where a component import is used both in a type context in the frontmatter (such as via typeof in a generic type argument) and as a JSX component in the template, linting must complete with no diagnostics and no fixes applied.

*   Linting an Astro file with experimentalFullSupportEnabled and the useImportType rule must produce the output 'No fixes applied' when all component imports are correctly used as rendered elements in the template section.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.