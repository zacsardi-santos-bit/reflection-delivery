## Description

When writing components for Solid.js, a common mistake is to destructure props directly in the component's function parameter. This breaks Solid's fine-grained reactivity system: Solid tracks reactive dependencies at the moment values are accessed, so destructuring at function entry copies the values out of their reactive containers and prevents updates from being tracked.

There is currently no automated way in Biome to catch this pattern. We need a new lint rule in the nursery group that detects when a JSX component function destructures its props parameter.

## Expected Behavior

The rule should warn developers when:
- An arrow function assigned to a PascalCase name uses object destructuring in its single parameter
- All forms of destructuring should be caught: simple properties, aliased properties, computed key properties, default values, rest elements, and TypeScript-typed parameter destructuring
- Each destructured variable that is used in a JSX attribute should produce a diagnostic pointing to the usage location, with a secondary note pointing back to the destructuring site
- An empty destructuring pattern should also be flagged

The rule should NOT warn when:
- The function receives a plain (non-destructured) props object
- The function has more than one parameter (not a component)
- Destructuring happens inside the function body rather than in the parameter list
- Destructuring occurs in a nested inner function inside a component
- A plain JSX element is declared (not a function)

## Diagnostic messages

The rule should tell users what went wrong and how to fix it, specifically directing them to use property accesses on the props object to preserve reactivity.

## Why This Matters

Without this rule, Solid developers can silently break reactivity by writing code that looks syntactically fine but loses reactive tracking. This is a very common beginner mistake when coming from React, where destructuring props is idiomatic. Having a lint rule catch this early improves developer experience and prevents subtle bugs that are hard to debug.
