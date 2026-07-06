## Description

The Cairo documentation tooling needs two improvements: cleaner item signature formatting and the ability to identify type references within signatures with their precise text positions.

Currently, the signature formatter has several inconsistencies:
- Functions whose return type is the empty tuple display a redundant annotation in their signature that should be omitted
- Impl block and extern type declarations are missing the expected trailing semicolons
- Function signatures with many parameters can wrap across multiple lines in an unhelpful way

In addition, there is no way to query the formatted signature of a documentable item and simultaneously learn which spans in that signature correspond to referenced types. This makes it impossible for documentation renderers to add hyperlinks from type names to their definitions.

## Expected Behavior

- The documentation system should expose a way to retrieve both the formatted signature and a list of character-range markers for every type name referenced in that signature
- When a referenced type cannot be resolved (e.g., the type was never declared), the signature should show a placeholder token instead of crashing or leaving the field blank, and no link should be emitted for it
- Impl declarations and extern type declarations should include a trailing semicolon in their signatures
- Functions returning the unit type should not show the return type in their signature
- Function parameter lists should always be presented on a single line

## Why This Matters

Documentation tools that auto-generate API reference pages need both clean signatures and machine-readable link positions so they can cross-reference types without post-processing the text manually. This change enables that use-case while also fixing cosmetic signature inconsistencies visible to end users.
