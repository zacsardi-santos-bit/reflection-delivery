## Description

There is currently no way to observe form state changes outside of a React component. If you want to react to events like a field becoming dirty, values changing, or validation completing, you must be inside a React component using the form hooks — and that necessarily causes React re-renders. This makes it difficult to integrate with non-React code, or to optimize performance when you want side effects without triggering component re-renders.

## Expected Behavior

- It should be possible to create a standalone form control and subscribe to its state changes via a callback, without being inside a React component.
- The subscription should allow specifying which aspects of form state (e.g. dirty state, values, validity) to observe.
- The subscription should support filtering by field name, so the callback only fires when a specific field is involved.
- The callback should receive the complete current form state at the time of the change, including which field triggered it.
- When a standalone form control is shared with a React component, the component should only re-render for state changes it explicitly opts into — external subscriptions should not force React re-renders on their own.

## Why This Matters

This enables non-React integrations, analytics callbacks, and performance-sensitive patterns where you need to act on form state changes without coupling the reaction to the React render cycle. It also lets you share a single form control between React and non-React code.
