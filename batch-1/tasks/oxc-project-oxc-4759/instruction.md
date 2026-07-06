Implement a plugin for the oxc minifier that automatically injects import statements for globally-referenced identifiers based on a configuration. Ensure that the plugin supports default imports, named imports, and namespace imports, and handles compound property access patterns correctly.

*   Implement the `InjectGlobalVariablesConfig` struct:
    *   Define it in `crates/oxc_minifier/src/plugins/inject_global_variables.rs`.
    *   Implement the `new` method to accept a `Vec<InjectImport>` and return an `InjectGlobalVariablesConfig` instance that implements `Clone`.

*   Implement the `InjectImport` struct:
    *   Define it in `crates/oxc_minifier/src/plugins/inject_global_variables.rs`.
    *   Implement `named_specifier` method:
        *   Accept parameters `source: &str`, `imported: Option<&str>`, and `local: &str`.
        *   Return an `InjectImport` that generates:
            *   `'import { default as <local> } from <source>'` if `imported` is `None`.
            *   `'import { <name> as <local> } from <source>'` if `imported` is `Some(name)`.
    *   Implement `namespace_specifier` method:
        *   Accept parameters `source: &str` and `local: &str`.
        *   Return an `InjectImport` that generates `'import * as <local> from <source>'`.

*   Implement the `InjectGlobalVariables` struct:
    *   Define it in `crates/oxc_minifier/src/plugins/inject_global_variables.rs`.
    *   Implement the `new` method:
        *   Accept an `Allocator` reference and an `InjectGlobalVariablesConfig`.
        *   Return a builder instance.
    *   Implement the `build` method:
        *   Accept mutable references to a `SymbolTable`, a `ScopeTree`, and a `Program`.
        *   Inject import declarations at the top of the program for each unbound identifier matching a configured `InjectImport`.
        *   Ensure no duplicate imports are injected for identifiers already declared or shadowed in the source.
        *   Handle dotted keypath identifiers by generating a synthetic local variable and replacing member expression usages.
        *   Ensure the more specific keypath takes precedence when multiple patterns match.
        *   Trigger injection for shorthand object property values, but not for shorthand destructuring binding keys.
        *   Trigger injection for default values in function parameter destructuring.
        *   Avoid injection for identifiers used as object property keys, class method names, or export specifiers.
        *   Use double quotes for module source strings containing single quotes.

*   Export `InjectGlobalVariables`, `InjectGlobalVariablesConfig`, and `InjectImport` publicly from the `oxc_minifier` crate.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.