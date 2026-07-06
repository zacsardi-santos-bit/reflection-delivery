I'm using the oxc formatter with both JSDoc comment formatting and import sorting enabled, but it panics whenever I run it on a file that has JSDoc comments before the import block.

*   JsdocOptions must be publicly exported from the oxc_formatter crate so it can be imported directly as `oxc_formatter::JsdocOptions`.

*   JsdocOptions must implement the Default trait, allowing callers to construct it with JsdocOptions::default().

*   FormatOptions must have a `jsdoc` field of type `Option<JsdocOptions>`. Setting this field to `Some(JsdocOptions::default())` enables JSDoc comment formatting.

*   When both JSDoc formatting (jsdoc: Some(JsdocOptions::default())) and import sorting (sort_imports: Some(...)) are enabled, the formatter must not panic when the input contains a single-line JSDoc comment followed by import declarations. The JSDoc comment must be formatted (e.g., first word capitalized) and imports must be sorted alphabetically.

*   When both JSDoc formatting and import sorting are enabled, the formatter must not panic when the input contains a multi-line JSDoc comment (including comments with JSDoc tags such as @see) followed by import declarations. The multi-line JSDoc comment must be formatted and imports must be sorted alphabetically.

*   When only import sorting is enabled and the input contains a trailing non-import comment that appears after the last import declaration, the formatter must not panic and must preserve the imports and trailing comment correctly in the output.


*   Interface details: Type: Struct
Name: JsdocOptions
Location: crates/oxc_formatter/src/options.rs (or wherever JsdocOptions is defined)
Description: Options controlling JSDoc comment formatting behavior. Must be publicly re-exported from the oxc_formatter crate root so it is accessible as `oxc_formatter::JsdocOptions`. Must implement the `Default` trait.
Signature: JsdocOptions::default() -> JsdocOptions

Type: Field
Name: jsdoc
Location: FormatOptions struct (crates/oxc_formatter/src/options.rs or similar)
Description: An optional field on the existing `FormatOptions` struct that enables JSDoc comment formatting when set to `Some(JsdocOptions)`. Type: `Option<JsdocOptions>`. Setting this field to `Some(JsdocOptions::default())` activates JSDoc formatting.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.