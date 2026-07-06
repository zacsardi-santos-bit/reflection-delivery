I'm seeing false positive warnings from the variable shadowing lint rule in my project.

*   The is_builtin_global_name function must return true if the given name is explicitly enabled via the context's custom globals (ctx.globals().is_enabled(name)).

*   The is_builtin_global_name function must iterate only over the environments that are explicitly configured (ctx.env()) and return true only if the name exists in one of those environments' global lists.

*   The is_builtin_global_name function must NOT consult global lists from unconfigured environments; checking all environments' globals regardless of configuration is incorrect.

*   When builtinGlobals is true and the browser environment is enabled, variables whose names are not present in the browser environment's global list (such as 'Form' and 'removeFile') must NOT be flagged as shadowing a built-in global.

*   When builtinGlobals is true and both browser and node environments are enabled, variables whose names are not present in either environment's global list (such as 'Form' and 'removeFile') must NOT be flagged as shadowing a built-in global.

*   When builtinGlobals is true and the browser environment is enabled, variables whose names ARE present in the browser environment's global list (such as 'top' and 'window') MUST be flagged as shadowing a built-in global.

*   When builtinGlobals is true and both browser and node environments are enabled, variables whose names ARE present in those environments' global lists (such as 'window' for browser and 'process' for node) MUST be flagged as shadowing a built-in global.

*   Tests for browser-specific global shadowing must configure the browser environment explicitly; test cases checking browser globals without an environment config must be updated to use env: { browser: true }.


*   Interface details: Type: Function
Name: is_builtin_global_name
Location: crates/oxc_linter/src/rules/eslint/no_shadow/mod.rs
Signature: is_builtin_global_name(ctx: &LintContext, name: &str) -> bool
Description: Determines whether a given variable name is a built-in global. Must return true only if the name is either explicitly enabled via ctx.globals(), or if it exists in the globals list of one of the environments configured in ctx.env(). Must NOT return true simply because the name exists in some environment's globals that is not configured.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.