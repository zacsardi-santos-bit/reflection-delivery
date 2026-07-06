## Description

The lint rule that enforces correct placement of React hooks produces false positives when React components are defined inside test function bodies. If a developer writes a test that creates a React component inline — whether as a named function declaration or as an arrow function assigned to a variable — and uses hooks at the top level of that component, the linter incorrectly flags those hooks as violations.

Similarly, hooks passed directly as callbacks to test rendering helpers are also being incorrectly flagged, even though those patterns are entirely valid.

## Expected Behavior

- Hooks called at the top level of a React component defined inside a test function body should be considered valid and should produce no lint diagnostic.
- Hooks used inside testing-helper callbacks (e.g. passed to a render-hook utility) should produce no lint diagnostic.
- Hooks that are genuinely called from inside a nested, non-component inner function within a React component should still be flagged as a violation, even when that component lives inside a test function.

## Why This Matters

Testing React components inline is a common and valid pattern. The current behavior causes developers to see spurious lint errors in their test files, forcing them to either suppress the rule or restructure their tests in unnatural ways. Fixing this allows the linter to accurately distinguish between valid hook usage and real violations, even in test code.
