## Description

The ESLint-to-Biome migration tool does not handle several widely used lint rules when converting configurations. Specifically, rules from the jest, vitest, jsx-a11y, unicorn, and typescript-eslint plugin ecosystems that carry user-defined options are either ignored or produce incomplete output during migration. This means developers who run the migration command end up with a Biome configuration that is missing rules they previously relied on, forcing them to manually re-configure those rules after migration.

## Expected Behavior

When migrating an ESLint configuration that includes any of the following rule families, the tool should produce a Biome configuration that includes the equivalent Biome rule at the same severity level, with the user's custom options faithfully translated:

- Rules for enforcing consistent test function naming (from both jest and vitest plugins), including preferred function names inside and outside of describe blocks
- Rules for enforcing valid ARIA roles on JSX elements, including options for allowing specific invalid roles and ignoring non-DOM elements
- Rules for restricting the use of global variables, preserving any custom messages associated with each restricted global
- Rules for enforcing a consistent array type syntax, including the preferred syntax style
- Rules for enforcing a consistent type import style, including the inline vs. separate import preference
- Rules for requiring explicit member accessibility modifiers on class members
- Rules for enforcing naming conventions across selectors like properties, interfaces, enum members, and variables
- Rules for enforcing filename casing conventions, including support for specifying multiple allowed casing styles

## Why This Matters

Projects migrating from ESLint to Biome currently lose their configuration for these rules and must manually reconstruct them in the Biome format. Automating this translation reduces the migration burden and ensures no lint coverage is silently dropped during the switch.
