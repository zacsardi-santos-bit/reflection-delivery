## Description

The UI component library needs a standard, reusable foundation for building components that can declaratively describe their props, styles, and behaviors in a single configuration object. Without this, each component has to manually handle many concerns independently: separating typed component props from passthrough HTML attributes, applying default values for optional props, supporting responsive variants that take different values at different viewport sizes, mapping props to utility-based CSS classes and CSS custom properties, managing background context inheritance between container and child components, wiring up analytics tracking, and resolving navigation links relative to the current route.

## Expected Behavior

- A set of helper utilities must be available for resolving responsive prop values across a breakpoint scale (from small to large), with proper fallback to the nearest defined smaller breakpoint and fall-forward when nothing is defined below the current size
- A helper for separating a component's own props from passthrough attributes must properly handle defaults (preserving explicitly provided falsy values over defaults), resolve responsive values, and strip utility props from HTML passthrough
- A utility helper must convert utility prop values into the appropriate CSS class names and CSS custom properties, including support for predefined token values, arbitrary custom values, responsive variants with breakpoint prefixes, transform functions for specific props, and multi-prop combinations
- A React hook must combine all of the above: given a component's definition config and the user's props, it must return resolved own props (with a class map for each component slot), passthrough rest props, data attributes, utility styles, and optionally analytics tracking
- The hook must support configuring which component slot receives user-provided class names and which slot receives utility classes, with the ability to disable either entirely
- Background context must propagate correctly: provider components wrap their children in context with the resolved background value (incrementing neutral levels from parent context); consumer components read from context and expose it as a data attribute
- Navigation links provided as relative paths must be resolved against the current route when inside a router context; external URLs and protocol-relative URLs must be left unchanged

## Why This Matters

This infrastructure eliminates repeated boilerplate from every UI component and establishes consistent, predictable behavior for prop handling, responsive styling, background-aware theming, analytics integration, and navigation across the entire component library.
