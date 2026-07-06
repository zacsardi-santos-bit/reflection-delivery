## Description

The unused variable lint rule is producing incorrect diagnostic messages for rest parameters (spread-style function parameters) in TypeScript code. There are two distinct problems:

1. **Wrong "used" classification for type-only usages**: When a rest parameter's name appears only in a type annotation — either as a reference in the parameter's own type (like a self-referencing type query) or in a return type predicate — the lint rule reports the parameter as never used. It should instead classify the parameter as being used only in a type context, which is the correct and more helpful classification.

2. **Wrong ignore pattern applied**: When developers configure a custom ignore pattern specifically for function arguments, rest parameters are not picking up that argument-specific pattern. Instead, the diagnostic message references a different (variable-level) ignore pattern, resulting in a confusing and incorrect suggestion for how to name the parameter.

## Expected Behavior

- A rest parameter whose name appears only in a type query within its own type annotation should produce a diagnostic classifying the parameter as type-only usage, with a suggestion to rename it following the convention for unused parameters.
- A rest parameter whose name appears only in a return type predicate should produce the same type-only usage diagnostic.
- A rest parameter that doesn't match the argument ignore pattern should produce a diagnostic that mentions the correct argument ignore pattern in the suggestion, not the variable ignore pattern.

## Why This Matters

Developers using TypeScript rest parameters in combination with type annotations or argument-specific ignore pattern configurations are getting misleading lint messages. The incorrect "never used" classification and wrong pattern reference make the diagnostic output inaccurate and confusing, potentially causing developers to suppress or misinterpret the warnings.
