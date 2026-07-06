## Description

The PatternFly React templates library currently provides a multi-selection checkbox select component but is missing ready-to-use template components for two very common UI patterns: a simple dropdown menu and a simple single-selection select. Developers who want to use these patterns must compose them from low-level primitives, which involves a lot of repetitive boilerplate. Additionally, the existing checkbox select template has a hardcoded toggle width and no way to pass extra properties to the toggle button, limiting how much it can be customized.

## Expected Behavior

- A new simple dropdown template component should be available, supporting toggle content, disabled state, toggle variant styling, configurable toggle width, extra toggle props, an accessible label for the toggle, open/close and selection callbacks, full-width toggle mode, focus management after selection, and a list of items that can be plain actions, links, dividers, or disabled.
- A new simple single-selection select template component should be available, with a default "Select a value" placeholder, configurable toggle width (defaulting to 200px), extra toggle props, open/close and selection callbacks, and support for disabled state and disabled individual options.
- The existing checkbox select template should be updated so that the toggle width is configurable (not hardcoded) and extra props can be passed to the toggle button. Its internal container should no longer carry a fixed identifier attribute.

## Why This Matters

Without these templates, teams have to write significant boilerplate every time they need a dropdown or a basic select. Providing these as first-class template components lowers the barrier to using the design system correctly, promotes consistency across applications, and reduces duplicated code.
