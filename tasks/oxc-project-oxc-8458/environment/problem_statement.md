# TypeScript Namespace Transformer Drops Used Import Aliases

## Description

The TypeScript-to-JavaScript transformer incorrectly removes all namespace-local import alias declarations from namespace bodies, even when those aliases are actively used as values within the namespace. This produces broken JavaScript output, because any code that references the alias will fail at runtime — the variable binding simply does not exist in the output.

TypeScript namespaces support an aliasing syntax for creating local shorthand references to values from an outer scope. The transformer should respect this: if the alias is used as a value inside the namespace, it must be kept (and converted to an ordinary variable declaration). If it is unused or only appears in a type annotation, it should be removed.

## Expected Behavior

- If a namespace-local import alias is referenced as a **value** anywhere in the namespace body, it must be preserved in the output as a variable declaration.
- If a namespace-local import alias is **not referenced** (or only appears in type positions), it must be removed from the output.
- When the transformer is configured with the option to preserve non-type imports, **all** namespace-local import aliases must be kept unconditionally — even those that are never referenced or only used as type annotations.
- Type annotations on variable declarations in the namespace must always be stripped, even when the associated import alias is preserved.

## Why This Matters

Without this fix, namespaces that use import aliases as runtime values produce subtly incorrect JavaScript. The alias disappears from the output, causing a runtime reference error wherever the alias was used. Developers who rely on TypeScript namespaces with this pattern currently get silently broken code after transformation.
