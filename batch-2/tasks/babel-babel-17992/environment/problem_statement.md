## Description

When Babel transforms bundled JavaScript that comes with an external source map, the output source map loses track of the original variable names for identifiers that were already tracked in the input source map's name table.

This is especially noticeable when a transform renames variables. If the input source map already records that a certain identifier maps to a specific original name, and a Babel plugin then renames that variable (for example, to avoid a naming conflict in the same scope), the resulting source map should still trace the renamed variable back to its original name. Instead, the original name attribution from the input source map is silently discarded.

## Expected Behavior

- When processing code with an external source map that tracks variable names, the output source map should include those original names and correctly map all renamed output identifiers back to them.
- When two variables in different scopes share the same original name and must be renamed differently to avoid conflicts (one keeping the base name, another getting a disambiguating suffix), the source map should correctly associate each renamed identifier with the common original name from the input source map.
- The full chain of name attribution should be preserved: output identifier → renamed identifier → original identifier in pre-bundled source.

## Why This Matters

Developers relying on source maps to debug or trace transformed code back to its origin need accurate name mappings. When Babel consumes bundled output as input (as is common in certain toolchain setups), losing the original variable name attributions breaks the end-to-end source map chain, making it impossible to reliably trace transformed identifiers back to the original source symbols.
