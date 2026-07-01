## Description

When the Gleam compiler reports an inexhaustive case expression error, it lists the missing patterns that the developer needs to handle. Currently, those missing patterns are displayed using the raw internal constructor names rather than the names that are actually accessible in the developer's current scope.

This is confusing and unhelpful when:
- A module was imported under an alias — the error shows the original name, not the alias.
- A constructor was imported unqualified — the error may show a module-qualified name that can't be used.
- A constructor was imported with a local alias — the error shows the original name instead of the alias.
- A constructor requires a module qualifier because a local type shadows it — the error may show an ambiguous unqualified name.

## Expected Behavior

When the compiler lists missing patterns in an inexhaustive case expression error, it should:
- Use the same qualifier (or alias) that the developer would use to write that pattern in their code
- Show unqualified names for unqualified imports
- Show aliased module names when the module was imported under an alias
- Show aliased constructor names when constructors were imported with a rename
- Show the appropriate module qualifier when it is needed to disambiguate from a locally shadowed name
- Work correctly for both user-defined types and built-in prelude types

## Why This Matters

When the compiler suggests patterns to handle, the developer should be able to copy them directly into their code. If the suggested pattern uses a name that isn't in scope or isn't what the developer calls it, they have to do extra mental work to figure out what to write. The error message should always suggest patterns using the exact syntax that would be valid in that module.
