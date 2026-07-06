Implement an enhancement to the floating-promises lint rule to detect unhandled promises when a function's return type is a conditional type alias. Ensure the rule treats conditional type aliases as unions of their possible outcomes, allowing for proper detection of potential Promise types.

*   Update the floating-promises lint rule to:
    *   Detect and flag calls to functions with a return type defined as a TypeScript conditional type alias (e.g., 'T extends U ? V : W') where at least one branch resolves to a Promise.
    *   Produce the diagnostic message: 'A "floating" Promise was found, meaning it is not properly handled and could lead to ignored errors or unexpected behavior.' and hint: 'This happens when a Promise is not awaited, lacks a `.catch` or `.then` rejection handler, or is not explicitly ignored using the `void` operator.' when such functions are called without handling the result.
*   Ensure the rule continues to:
    *   Detect arrays of Promises from mapping methods with async callbacks or callbacks returning a Promise.
    *   Flag them with the diagnostic: 'An array of Promises was found, meaning they are not properly handled and could lead to ignored errors or unexpected behavior.' and hint: 'This happens when an array of Promises is not wrapped with Promise.all() or a similar method, and is not explicitly ignored using the `void` operator.'
    *   Offer an unsafe fix in async function contexts with the label 'Unsafe fix: Wrap in Promise.all() and add await operator.' to wrap the expression with Promise.all() and prepend the await operator.
*   Modify the type inference system in `crates/biome_js_type_info/src/local_inference.rs`:
    *   Change the `TypeData` implementation's handling of the `TsConditionalType` AST node variant.
    *   Ensure it returns a union of both the true-branch and false-branch types instead of an opaque unknown type.
    *   Propagate this union-based resolution through type alias expansion to enable Promise detection in conditional type aliases.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.