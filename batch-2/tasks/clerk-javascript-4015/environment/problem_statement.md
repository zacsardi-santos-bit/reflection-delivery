## Description

The appearance customization system in the UI package has two related issues that need to be fixed.

First, the default appearance configuration is currently not accessible to code outside the module where it is defined. This makes it impossible for consumers — including tests and application code — to reference the default appearance values as a baseline or use them for comparisons.

Second, there is a mutation bug in the appearance merging logic. When element style overrides are applied, the implementation spreads a reference to the base theme object rather than creating an independent copy. This means that applying customizations modifies the shared default theme in-place. As a result, subsequent renders or nested appearance providers can unexpectedly inherit merged styles from previous overrides, producing incorrect accumulated class names.

## Expected Behavior

- The default appearance configuration should be publicly accessible as a named export so external code can reference it.
- Applying element style overrides via nested providers should produce a correctly merged result without modifying the original theme.
- When multiple nested appearance providers each supply class names for the same element, all classes should be accumulated in order (outer to inner), appended after the default class.
- The merged appearance state from multiple nested providers should include layout information in the user-provided overrides summary.

## Why This Matters

These issues can cause subtle rendering inconsistencies in applications that layer multiple appearance customizations, such as embedding a component inside a custom-styled wrapper. Without the fix, later renders may pick up stale or incorrect merged styles from a previous customization cycle.
