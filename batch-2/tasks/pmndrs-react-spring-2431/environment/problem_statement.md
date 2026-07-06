## Description

The animation library's context component has a bug that surfaces when used in React's strict development mode. Strict development mode intentionally simulates a component unmount and remount on every initial mount to help developers find side-effect bugs. When this lifecycle cycle occurs, the library incorrectly handles the cleanup: stopping an animation that was never started causes the spring's internal state to be corrupted — specifically, it establishes a goal target where none existed, based on the current value rather than leaving the state untouched.

As a result, when the component remounts after the simulated unmount, the animation context does not replay its expected sequence of updates. Users working with the animation context will see animations that start from incorrect positions, miss lifecycle callbacks, or behave inconsistently between development (strict mode) and production.

## Expected Behavior

- Stopping a spring that has never started must not modify its goal target. If no target was ever set, stopping the spring should leave it in its original unstarted state.
- When the animation context mounts in strict development mode, it must produce the full expected sequence of context updates: the user-configured animation target and callback first, then a default property broadcast, then the user-configured update again.
- All spring animation hooks must be compatible with strict mode's double-invocation behavior, with props functions called twice per render pass.

## Why This Matters

React encourages the use of strict mode in development, and many projects have it enabled by default. A library that behaves differently or incorrectly under strict mode is effectively broken for a large portion of users. Fixing this ensures animations behave consistently in both development and production environments.
