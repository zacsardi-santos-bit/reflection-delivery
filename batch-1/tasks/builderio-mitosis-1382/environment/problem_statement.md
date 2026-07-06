## Description

The SolidJS output from the cross-framework component compiler does not correctly handle computed/derived state values or reactive effect dependencies. Both are emitted as plain functions, but SolidJS's fine-grained reactivity system requires them to be wrapped in memoized reactive computations for proper change tracking.

Specifically:

- **Getter-style computed state** is emitted as a plain function declaration. In SolidJS, computed values should use a memoized reactive primitive so that dependent parts of the component tree are notified when the derived value changes.
- **Effect dependency expressions** are passed directly into the reactive effect tracking call without being wrapped in memos. SolidJS needs each dependency in an effect's dependency list to be a stable reactive computation, not a raw expression — otherwise change tracking may not work as expected.
- The memoization primitive is not included in the SolidJS import statement even though it is now required for both of the above cases.

## Expected Behavior

- Getter-type state properties should be emitted as memoized computations using the appropriate SolidJS reactive primitive.
- Each dependency expression in a reactive update hook should be extracted into a named intermediate memoized variable before the hook handler function, and the effect should reference these intermediate variables.
- The memoization primitive should be included in the SolidJS import list for any component that has signal-based state or reactive update hooks.

## Why This Matters

Generated SolidJS code is currently missing correct reactivity wiring for computed values and effect dependencies. This can cause components to silently fail to update when their derived state changes or when their effect dependencies change, leading to stale UI and subtle bugs in production applications.
