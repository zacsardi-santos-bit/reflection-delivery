I'm getting false positive "undeclared variable" lint errors in my Svelte components.

*   The lint rule for undeclared variables must correctly handle Svelte snippet parameters that use default values: variables bound by those parameters must be treated as declared within the snippet's scope, not flagged as undeclared.

*   A plain snippet parameter with a default value must register the parameter variable as declared within the snippet. For example, a parameter like 'image = fallback' declares 'image'.

*   An object destructuring snippet parameter with a default value must register all destructured variable names as declared. For example, '{ src, caption } = fallback' declares both 'src' and 'caption'.

*   An array destructuring snippet parameter with a default value must register all destructured element variables as declared. For example, '[first, second] = emptyList' declares both 'first' and 'second'.

*   A nested object destructuring snippet parameter with a default value must register the innermost bound variables as declared. For example, '{ item: { src } = fallback }' declares 'src'.

*   A nested array destructuring snippet parameter with a default value must register all innermost bound variables as declared. For example, '[{ id } = fallback]' declares 'id'.

*   A rest-pattern object destructuring snippet parameter with a default value must register both the non-rest properties and the rest variable as declared. For example, '{ src, ...rest } = fallback' declares both 'src' and 'rest'.

*   Variables used inside a snippet body that are not bound by any snippet parameter and are not declared in any outer scope (e.g., the component script block) must still produce a 'lint/correctness/noUndeclaredVariables' diagnostic with the message 'The X variable is undeclared.' where X is the name of the undeclared variable.

*   Variables from the component script block that are used as default values in snippet parameters must not produce false-positive undeclared variable diagnostics.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.