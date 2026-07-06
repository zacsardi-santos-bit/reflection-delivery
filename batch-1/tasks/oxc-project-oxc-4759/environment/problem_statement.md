# Add Global Variable Injection Plugin to Minifier

## Description

Many JavaScript build pipelines need to automatically inject import statements for globally-referenced identifiers — replacing bare usages of things like a DOM utility or a Promise polyfill with proper module imports at the top of the file. Currently the minifier has no equivalent of this capability, which is commonly provided by tools like Rollup's inject plugin.

## Expected Behavior

- When source code uses an identifier that matches a configured injection mapping, the tool should automatically prepend the appropriate import declaration.
- Supports default imports, named imports, and namespace imports, configurable per identifier.
- For compound member expression patterns (e.g., accessing a sub-property of a namespace), the tool should replace the full expression with a generated local variable and inject an import for it.
- When an identifier is already imported or locally declared in the source, no duplicate import should be injected.
- When an identifier is shadowed by a local parameter or binding in a given scope, injection should be skipped in that scope.
- Identifiers used as object property keys, class method names, or export specifiers rather than as value references should not trigger injection.
- Shorthand object property values should trigger injection, but shorthand destructuring binding keys should not.
- When multiple configured patterns could match (e.g. a namespace and a sub-property), the more specific match should take precedence.
- Module source strings that contain single quotes must be safely delimited.

## Why This Matters

Without this capability, developers cannot rely on the oxc toolchain for codebases that depend on automatic import injection, requiring them to use other bundlers or add manual workarounds. Implementing this plugin brings oxc's minifier to feature parity with widely used bundler plugins for this common transformation.
