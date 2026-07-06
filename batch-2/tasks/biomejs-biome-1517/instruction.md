Fix the lint rule for React hook placement to prevent false positives in test files. Ensure that hooks used at the top level of React components defined inside test functions are not flagged, while maintaining correct flagging for hooks improperly nested within inner functions.

*   Update the lint rule for hook placement:
    *   Do not flag hooks called at the top level of a React component defined as a function declaration inside a test function body.
    *   Do not flag hooks called at the top level of a React component defined as an arrow function assigned to a variable inside a test function body.
    *   Do not flag hooks called inside an anonymous arrow function passed as a callback to a rendering-helper invocation (e.g., `renderHook`) inside a test function body.
    *   Flag hooks called inside a named nested function within a React component, even if the component is defined inside a test function body.
        *   Use the diagnostic message: 'This hook is being called from a nested function, but all hooks must be called unconditionally from the top-level component.'
        *   Include the informational note: 'For React to preserve state between calls, hooks needs to be called unconditionally and always in the same order.'
        *   Reference: 'See https://reactjs.org/docs/hooks-rules.html#only-call-hooks-at-the-top-level'.
*   Implement the function `is_react_component` in `crates/biome_js_analyze/src/react/hooks.rs`:
    *   Signature: `is_react_component(name: &str) -> bool`
    *   Return `true` if the function name starts with an uppercase letter.
*   Implement the function `is_react_hook` in `crates/biome_js_analyze/src/react/hooks.rs`:
    *   Signature: `is_react_hook(name: &str) -> bool`
    *   Return `true` if the function name starts with "use" followed by an uppercase letter.
*   Implement the function `binding` in `crates/biome_js_syntax/src/union_ext.rs` (impl `AnyJsFunction`):
    *   Signature: `binding(&self) -> Option<AnyJsBinding>`
    *   Return the binding identifier for function declarations and export-default declarations, or the variable for arrow functions and function expressions. Return `None` if no binding exists.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.